title: shadowsocks-win-4.1.6 无法启动
date: 2019-04-20 15:07:32
desc: 
tags: [配置] 
---

> 简单记录一下配置，以免找不着。

新切换到一个win虚拟机，准备一下开发环境。可是没能访问Google，实在是憋屈。
众所周知，ss是用来干啥的。所以就在Github上找了一下[客户端](https://github.com/shadowsocks/shadowsocks-windows/releases)下载安装。
其实是免安装的，直接打开就报下面的错了：

<img src="{% asset_path 1.jpg %}" alt="APP CRASH" />

有兴趣了解处理细节的，可以点`...more`看下去。
想快速处理的，可以直接看解决方案，也就是补充安装对应依赖库即可：
* https://dotnet.microsoft.com/download/dotnet-framework/net472
* https://www.microsoft.com/en-us/download/details.aspx?id=48145

<!-- more -->

继续小剧场...

看到报错心凉了半截。毕竟面向搜索引擎编程，又不能打开Google，慌慌的。
但回头一想，以前ss也没这么弱呀~ 一般都是打开就行了，哪有这么容易就CRASH的。
结合当前的系统状况想了想，猜测是缺了库的原因。当前的系统十分干净，连个QQ都没有。而且现在软件开发都不是自己重造轮子，肯定依赖一些流行的框架或者库，例如glibc、.NET Framework等。

沿着这个思路，基本定位了Windows下两大必备库：
* .NET Framework
* Visual C++ distribution

那具体版本咧？基本就是瞎蒙的时候了。。
因为下载ss的版本号为4.1.6，对于当前来说已经比较新了。跟以前版本对比，旧版本基本不需要安装其他组件都能启动起来，但是4.x版本却无法启动。
于是估计为最新版本的：[.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework)，[C++ distribution 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)
结果还是不对 😅 。最后花了点时间逐个试了下，发现C++ distribution 2015版本就可以了 (￣▽￣)~* 

小剧场完。

<img src="{% asset_path 2.jpg %}" alt="APP DONE" width="466" height="474" />

后来连上ss才发现已有issue讨论了，供参考：https://github.com/shadowsocks/shadowsocks-windows/issues/2293