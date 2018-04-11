title: Java线程安全单例实现
date: 2018-04-09 14:02:25
desc: 
tags: [技术,代码] 
---

碰巧在网上浏览到，就一起集中在这里。

以前写单例就一种方式，双重校验锁。但是现在稍微扩展一下。

开始前，先总结单例三要素：
1. 私有构造函数
2. 私有静态持有自身对象
3. 公有静态获取方法

<!-- more -->

## 饥饿模式

缺点：在没有使用之前就已经初始化了。这就可能带来潜在的性能问题。

理解：类定义的final是为了单例不被继承修改么？

``` java
public final class EagerSingleton {

    private static EagerSingleton instance = new EagerSingleton();

    private EagerSingleton() {
        System.out.println("i'm eager!");
    }

    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

## 懒汉模式之双重检查锁

缺点：可能存在没有完全初始化的情况。 Ref.2

理解：这里做了点改进，使用了volatile关键字，是否可以保证instance在使用时不会存在没初始化完成的情况？

``` java
public final class DoubleCheckLazySingleton {

    private volatile static DoubleCheckLazySingleton instance = null;

    private DoubleCheckLazySingleton() {
        System.out.println("i'm double check lazy!");
    }

    public static DoubleCheckLazySingleton getInstance() {
        if (null == instance) {
            synchronized (DoubleCheckLazySingleton.class) {
                if (null == instance)
                    instance = new DoubleCheckLazySingleton();
            }
        }
        return instance;
    }
}
```

## 静态内部类

改进：对比**饥饿模式**，通过内部持有类实现延迟加载。这种写法最大的美在于，完全使用了Java虚拟机的机制进行同步保证，没有一个同步的关键字。

``` java
public final class HolderSingleton {
    private static class HolderSingletonContainer {
        private static final HolderSingleton instance = new HolderSingleton();
    }

    private HolderSingleton() {
        System.out.println("i'm holder!");
    }

    public static HolderSingleton getInstance() {
        return HolderSingletonContainer.instance;
    }
}
```

## 枚举法

> 网上评论，这是Effective Java推荐的实现方式

``` java 
interface SingletonInterface {
    void run();
}

public enum EnumSingleton implements SingletonInterface {
    INSTANCE {
        @Override
        public void run() {
            System.out.println("enum run!");
        }
    };

    private EnumSingleton() {
        System.out.println("i'm enum!");
    }
}
```

## 参考
1. [Hutool Wiki 单例工具-Singleton](http://hutool.mydoc.io/?t=255613)
2. [【转】线程安全的单例模式](https://my.oschina.net/looly/blog/152865)
3. [Java 单例模式的两种高效写法](https://www.jianshu.com/p/4e8ca4e2af6c)
4. http://stackoverflow.com/questions/26285520/implementing-singleton-with-an-enum-in-java
5. http://stackoverflow.com/questions/23721115/singleton-pattern-using-enum-version