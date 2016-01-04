title: hive udf
date: 2016-01-05 01:02:43
tags: [hive,大数据]
---

## 关于hive
* Hive是一种构建在Hadoop上的数据仓库
* Hive把SQL查询转换为一系列在Hadoop集群中运行的MapReduce作业，是MapReduce更高层次的抽象，不用编写具体的MapReduce方法
* Hive将数据组织为表，这就使得HDFS上的数据有了结构，元数据即表的模式，都存储在名为metastore的数据库中

## UDAF开发
1. 处理阶段解析
``` java
public static enum Mode {
    /**
     * PARTIAL1: 这个是mapreduce的map阶段:从原始数据到部分数据聚合
     * 将会调用iterate()和terminatePartial()
     */
    PARTIAL1,

    /**
     * PARTIAL2: 这个是mapreduce的map端的Combiner阶段，负责在map端合并map的数据::从部分数据聚合到部分数据聚合:
     * 将会调用merge() 和 terminatePartial() 
     */
    PARTIAL2,

    /**
     * FINAL: mapreduce的reduce阶段:从部分数据的聚合到完全聚合 
     * 将会调用merge()和terminate()
     */
    FINAL,

    /**
     * COMPLETE: 如果出现了这个阶段，表示mapreduce只有map，没有reduce，所以map端就直接出结果了:从原始数据直接到完全聚合
     * 将会调用 iterate()和terminate()
     */
    COMPLETE
};
```
2. 实现接口
``` java
import com.google.common.collect.Lists;
import org.apache.hadoop.hive.ql.exec.UDFArgumentTypeException;
import org.apache.hadoop.hive.ql.metadata.HiveException;
import org.apache.hadoop.hive.ql.parse.SemanticException;
import org.apache.hadoop.hive.ql.udf.generic.AbstractGenericUDAFResolver;
import org.apache.hadoop.hive.ql.udf.generic.GenericUDAFEvaluator;
import org.apache.hadoop.hive.ql.udf.generic.GenericUDAFParameterInfo;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspectorFactory;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspectorUtils;
import org.apache.hadoop.hive.serde2.objectinspector.StandardListObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.primitive.PrimitiveObjectInspectorFactory;
import org.apache.hadoop.hive.serde2.typeinfo.TypeInfo;
import org.apache.log4j.Logger;

import java.util.List;

/**
 * LinkedByKey
 *
 * @author tobin
 * @since 2015-12-15
 */
public class LinkedByKey extends AbstractGenericUDAFResolver {
    private static Logger LOG = Logger.getLogger(LinkedByKey.class);

    public static final int PARAMETER_NUMBER = 4;

    @Override
    public GenericUDAFEvaluator getEvaluator(GenericUDAFParameterInfo info) throws SemanticException {
        // 检查参数长度
        TypeInfo[] parameters = info.getParameters();
        if (parameters.length != PARAMETER_NUMBER) {
            throw new UDFArgumentTypeException(parameters.length - 1,
                    "it needs " + PARAMETER_NUMBER + " parameters");
        }

        // 检查参数类型
        for (int i = 0; i < PARAMETER_NUMBER; i++) {
            if (parameters[i].getCategory() != ObjectInspector.Category.PRIMITIVE) {
                throw new UDFArgumentTypeException(0, "only allow PRIMITIVE type, but get '" +
                        parameters[i].getTypeName() + "' on parameter " + (i + 1));
            }
        }

        return new LinkedEvaluator();
    }

    public static class LinkedEvaluator extends GenericUDAFEvaluator {

        private StandardListObjectInspector sloi;

        /**
         * 中间缓存操作
         *
         */
        // 中间结果容器
        static class RowBuffer extends AbstractAggregationBuffer {
            List<Object> row = Lists.newArrayList();
        }

        @Override
        public AggregationBuffer getNewAggregationBuffer() throws HiveException {
            RowBuffer rowBuffer = new RowBuffer();
            reset(rowBuffer);
            return rowBuffer;
        }

        @Override
        public void reset(AggregationBuffer buffer) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'reset' function");
            }

            RowBuffer rowBuffer = (RowBuffer) buffer;
            rowBuffer.row.clear();
        }

        /**
         * 初始化
         *
         * @param mode
         * @param objectInspectors
         * @return
         * @throws HiveException
         */
        public ObjectInspector init(Mode mode, ObjectInspector[] objectInspectors) throws HiveException {
            super.init(mode, objectInspectors);

            if (mode != Mode.PARTIAL1) {
                sloi = (StandardListObjectInspector) objectInspectors[0];
            }

            return ObjectInspectorFactory.getStandardListObjectInspector(
                    PrimitiveObjectInspectorFactory.javaStringObjectInspector
            );
        }

        /**
         * 处理流程
         *
         */
        public void insertRow(AggregationBuffer buffer, Object[] values) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'insertRow' function");
            }
            if (values.length != PARAMETER_NUMBER) {
                throw new HiveException("it needs " + PARAMETER_NUMBER + " parameters " +
                        "but get " + values.length + " " +
                        "of values in 'insertRow' function");
            }

            // 有序插入
            String key = values[0].toString();
            RowBuffer rowBuffer = (RowBuffer) buffer;
            int i;
            for (i = 0; i < rowBuffer.row.size(); i += PARAMETER_NUMBER) {
                String current = rowBuffer.row.get(i).toString();

                // TODO: 去重
                // 从小到大排序
                if (current.compareToIgnoreCase(key) > 0) {
                    for (int j = 0; j < PARAMETER_NUMBER; j++) {
                        rowBuffer.row.add(
                                i + j,
                                ObjectInspectorUtils.copyToStandardObject(
                                        values[j], PrimitiveObjectInspectorFactory.javaStringObjectInspector
                                )
                        );
                    }
                    break;
                }
            }
            // 如果前面无法插入, 插入到最后
            if (i >= rowBuffer.row.size()) {
                for (int j = 0; j < PARAMETER_NUMBER; j++) {
                    rowBuffer.row.add(ObjectInspectorUtils.copyToStandardObject(
                            values[j], PrimitiveObjectInspectorFactory.javaStringObjectInspector
                    ));
                }
            }
        }

        // 相当于mapper阶段
        @Override
        public void iterate(AggregationBuffer buffer, Object[] parameters) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'iterate' function");
            }
            if (parameters == null) {
                throw new HiveException("null parameters in 'iterate' function");
            }
            if (parameters.length != PARAMETER_NUMBER) {
                throw new HiveException("it needs " + PARAMETER_NUMBER + " parameters " +
                        "but get " + parameters.length + " " +
                        "of parameters in 'iterate' function");
            }
            if (parameters[0] == null) {
                throw new HiveException("null parameters[0] in 'iterate' function");
            }

            insertRow(buffer, parameters);
        }

        // 相当于combiner阶段
        @Override
        public Object terminatePartial(AggregationBuffer buffer) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'terminatePartial' function");
            }

            return terminate(buffer);
        }

        // 相当于reduce阶段
        @Override
        public void merge(AggregationBuffer buffer, Object row) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'merge' function");
            }
            if (row == null) {
                throw new HiveException("null row in 'merge' function");
            }

            List<Object> values = (List<Object>) sloi.getList(row);
            for (int i = 0; i < values.size(); i += PARAMETER_NUMBER) {
                List<Object> parameters = Lists.newArrayList();
                for (int j = 0; j < PARAMETER_NUMBER; j++) {
                    parameters.add(values.get(i + j));
                }

                insertRow(buffer, parameters.toArray());
            }
        }

        @Override
        public Object terminate(AggregationBuffer buffer) throws HiveException {
            if (buffer == null) {
                throw new HiveException("null buffer in 'terminate' function");
            }

            // TODO: 补全缺失的时间
            RowBuffer rowBuffer = (RowBuffer) buffer;
            return Lists.newArrayList(rowBuffer.row);
        }
    }
}
```

## 参考
1. http://www.cnblogs.com/ggjucheng/archive/2013/02/01/2888051.html

## FOOTER
Thanks For Reading!
