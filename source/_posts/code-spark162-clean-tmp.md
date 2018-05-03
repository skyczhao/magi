title: Spark 1.6.2 清理临时文件
date: 2018-04-28 12:23:06
desc: 
tags: [] 
---

写这篇之时，实际已经隔了好多天没更新。当时只留了个开头，就没有写下去。

现在也是抽空趁机把几篇同时更新。最近也是事多，工作上、生活上都或多或少产生一些问题。这个五一其实过得蛮拧巴。

不过写这件事情还是要坚持的。今天还翻看了耗子哥的博客-[CoolShell](https://coolshell.cn/)。像耗子哥这般能持续输出有深度有硬实力的硬文，是自己学习的典范。自己的写作还是过多水分和稀释了，缺乏真正的见解，不过有些经验还是得长久积累才有几乎写出来对不。

下面是这段时间写Spark时候遇到的一个问题总结，内容不深但是供参考。

<!-- more -->

## 问题状况
系统的磁盘被占满，导致系统上安装的Hadoop组件部分退出，以致集群某些服务不可用。

## 定位原因

> Spark本地临时目录没有清理

1. 观察目录使用情况，发现`/tmp`使用过多
2. 对`/tmp`下目录查看，发现以`spark-*`开头的文件夹空间占用很大
<img src="{% asset_path spark162-ct-01.png %}" alt="" width="370" height="37" />
3. 深入查看发现都为同一个项目的jar包

## 原理探究(待考证)

> 估计是主进程常驻，JavaSparkContext没有清理的原因

1. 起spark任务的主进程cron进程，常驻并定时调度起spark
2. 虽然spark context使用完后close，但主进程没有退出，spark context清理不完整

## 解决方案

关闭spark context的时候，同时主动调用清理临时目录的代码：
```java
public static void clearTmpDirs() {
    try {
        logger.info("clear Utils's localRootDirs");
        synchronized (Utils$.MODULE$) {
            Utils$.MODULE$.clearLocalRootDirs();
        }
        logger.info("deleting ShutdownHookManager's shutdownDeletePaths");
        Field field = ShutdownHookManager$.MODULE$.getClass()
                .getDeclaredField("org$apache$spark$util$ShutdownHookManager$$shutdownDeletePaths");
        field.setAccessible(true);
        scala.collection.mutable.HashSet shutdownDeletePaths =
                (scala.collection.mutable.HashSet) field.get(ShutdownHookManager$.MODULE$);
        synchronized (shutdownDeletePaths) {
            Set<String> dirPaths = new HashSet<>(JavaConversions.asJavaSet(shutdownDeletePaths));
            for (String dir : dirPaths) {
                File dirPath = new File(dir);
                logger.info("deleting Directory - " + dirPath);
                Utils.deleteRecursively(dirPath);
                ShutdownHookManager.removeShutdownDeleteDir(dirPath);
            }
        }
    } catch (Exception e) {
        logger.warn("get ShutdownHookManager's shutdownDeletePaths failed", e);
    }
}
``` 