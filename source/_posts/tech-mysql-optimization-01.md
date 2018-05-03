title: MySQL性能初步调优-01
date: 2018-04-25 21:44:52
desc: 
tags: [MySQL] 
---

MySQL是一个十分流行的RDBMS。介绍就不必了吧，相信大家都了解。除了有钱有需求需要购买使用商用版的SQLServer以及Oracle DB2之类的，MySQL基本承载大多数公司的关系型数据存储需求。开源、性能也不差，而且对开发者友好（基本没有多少程序员没听过或者没有用过）。

在MySQL的日常使用过程中，总会踩上一两个坑。随着项目推进，对MySQL的要求越来越高，也就会逐渐接触到各式隐藏问题。例如：中文字符集不兼容，表数据增长过快等。

网上对MySQL存在大量使用与优化的文章。正因为MySQL的广泛流行，不少热心的程序员都会或多或少分享自己遇到坑或者使用过程的优化心得。所幸于此，我平时面对的异常都有对应的解决方案文章。

这里就梳理最近遇到的问题，以及对应的处理方案。

<!-- more -->

## SELECT语句异常的慢

总结：表设计之初就应该预计好字段类型、大小，以及需要索引的字段。在扩展性与性能之间取得平衡，不能一味为着拓展性而忽略实际场景的需要。

问题描述：简单的select提取100+条数据，花了1分钟。按id取，数据表总行数约1000万。

原因分析：
1. 通过自带工具profile
    * `SET profiling = ON;`
    * `show profiles;`
    * `show profile for query [查询ID];`
2. 耗时主要集中在sending data阶段
    * 该阶段实际上包括了两个阶段：收集+发送
    * 从参考文章3中发现这个：当Innodb的存储格式是ROW_FORMAT=COMPACT(or ROW_FORMAT=REDUNDANT)的时候，Innodb只会存储前768字节的长度，剩余的数据存放到“溢出页”中
    * 于是怀疑是不是字段设置不合理导致的
3. 查看表结构，基本定位了原因是没有做索引。
    * id字段没有做索引
    * 字段的类型与大小脱离实际
    * 
```sql
CREATE TABLE `t_customer_tags_tmp` (
  `target_id` VARCHAR(128)
              COLLATE utf8mb4_unicode_ci DEFAULT NULL
  COMMENT '客户ID',
  `tags_list` VARCHAR(200)
              COLLATE utf8mb4_unicode_ci DEFAULT NULL
  COMMENT '部分标签列表',
  KEY `customer_tags_tmp_target_id_index`(`target_id`)
)
  ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4
  COLLATE = utf8mb4_unicode_ci
  COMMENT ='客户部分标签列表';
```
4. 改过来后，查询时间缩短为ms级
```sql
CREATE TABLE db_parrot.t_customer_tags_tmp (
  `target_id` BIGINT NOT NULL PRIMARY KEY
  COMMENT '客户ID',
  `tags_list` VARCHAR(64) DEFAULT NULL
  COMMENT '部分标签列表'
)
  COMMENT ='客户部分标签列表';
```

## 批量INSERT异常的慢

总结：`autocommit`的使用需要根据实际。一般来说，保证每次commit可能实现较高的数据一致性，但会牺牲性能；但如果需要等到多条结果完成后再commit，又需要注意数据一致的问题。

问题描述：在调用QueryRunner时，批量插入更新非常慢。1000多的数据需要耗时1分钟以上。

原因分析：
1. 面向Google编程，查了一下MySQL插入缓慢的原因
    * [已经验证] autocommit：每次插入都commit一次，虽然保证数据的写入，但数量较多时将导致插入时间大大增长
    * [尚未验证] innodb_flush_log_at_trx_commit：记录操作记录写入时机。
2. 在执行的MySQL工具中，配置批量插入的"autocommit"为"false"。
    * 最后必须补充commit。
3. 效果：更新后，插入时间缩短为毫秒级别。

## 两个小表关联异常的慢

总结：小表本来就不大，但是却消耗的大量时间(>20 min)

问题描述：3w多的客户跟标签，关联标签居然花了1分钟

原因分析：暂无

解决方案：添加索引
    
## LIMIT优化

总结：MySQL先执行连接查询，再进行排序。

！！！TBA


## 参考文献
1. [MySQL查询优化:连接查询排序limit(join、order by、limit语句)](https://blog.csdn.net/xiao__gui/article/details/8616224)
2. [实战：MySQL Sending data导致查询很慢的问题详细分析](http://blog.itpub.net/30150152/viewspace-1774408/)
3. [innodb使用大字段text，blob的一些优化建议](http://hidba.org/?p=551)
4. [［MySQL］MySQL分区与传统的分库分表](http://haitian299.github.io/2016/05/26/mysql-partitioning/)
5. [［MySQL］MySQL索引实现原理及相关优化策略](http://haitian299.github.io/2016/05/20/mysql-index-and-optimizing/)