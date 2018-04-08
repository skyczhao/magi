title: Android OpenCV开发环境配置
date: 2017-10-20 16:21:24
desc: 
tags: [配置] 
---

> 主要参考文档：https://docs.opencv.org/master/d5/df8/tutorial_dev_with_OCV_on_Android.html

1. 从[OpenCV.org](OpenCV.org)下载最新版 "OpenCV sdk for Android"，并解压
2. 在Android Studio中导入OpenCV：
    * `From File -> New -> Import Module`
    * 选择`sdk/java`目录
3. 更新导入的OpenCV模块中的`build.gradle`文件，以适配原项目的`build.gradle` 
    * compileSdkVersion 
    * buildToolsVersion 
    * minSdkVersion
    * targetSdkVersion.
4. 添加依赖模块：
    * 官方方式
        * `Application -> Module Settings`，选择`Dependencies`子页
        * 点击底部`+`，选择`Module Dependency`，并导入`OpenCV`模块
    * 博主的方式
        * ROOT/app/build.gradle
            * `compile project(':opencv’)`
        * ROOT/settings.gradle 需要把导入后的项目另存
            * `include ':opencv'`
            * `project(':opencv').projectDir = new File('/Users/Programs/dt-flyingpig-opencv')`
5. 导入OpenCV的库文件
    * 官方方式
        * 把OpenCV的库文件（`sdk/native`）放到`Android Studio`的`app/src/main`下
    * 博主的方式
        * ROOT/app/build.gradle
        ```
        sourceSets {
            main {
                jniLibs.srcDirs += [rootProject.ext.opencv.sdk + '/sdk/native/libs']
            }
        }
        ```
        * ROOT/build.gradle
        ```
        ext {
            opencv = [
                    sdk: '/Users/tobin/ProgramFiles/OpenCV-android-sdk'
            ]
        }
        ```
6. 在Android Studio中，重命名复制的库文件目录为`jniLibs`，即完成