title: 0.Android开工
date: 2018-09-16 21:18:00
desc: 
tags: [] 
---

这个补充下背景。
其实早前也做过一些安卓开发。是基于一个APP多开框架改的，自己加了脚本操作和验证码识别。
最近需要把验证码部分拿出来，但是却忘了怎么配置开发环境的。因为也个的比较久了。
所以就简单记录一下，当做开发笔记。
没啥技术的，看官看看就好。

## 创建项目
1. File -> New -> New Project
2. API 23: Android 6.0

<!-- more -->

## 坑~
1. 小米卸载刚安装的App后就无法再调试了？报错~
    * 问题描述： 
    ```
    Error while executing: am start -n "com.thirteenyu.whathappen/com.thirteenyu.whathappen.MainActivity" -a android.intent.action.MAIN -c android.intent.category.LAUNCHER
    Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.thirteenyu.whathappen/.MainActivity }
    Error type 3
    Error: Activity class {com.thirteenyu.whathappen/com.thirteenyu.whathappen.MainActivity} does not exist.
    
    Error while Launching activity
    ```

    * 解决：分身模式看看？是不是还有卸载了的APP？
        1. 双重卸载真身与分身里面的APP：虽然麻烦，但有效
        2. 删除分身数据：看分身对你有没有用咯~ 但这个是最彻底的
        
2. OpenCV For Android
    * 问题描述：
    ```
    Error: ... /Users/tobin/Library/Android/sdk/ndk-bundle/toolchains/mips64el-linux-android-4.9/prebuilt/darwin-x86_64/bin/mips64el-linux-android-strip ...
    ```
    * 原因分析：OpenCV libs下带有mips、x86等，实际没啥用的库
    * 解决：从lib删除这些libs，目前只保留：`arm64-v8a armeabi-v7a`

