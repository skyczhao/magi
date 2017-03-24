title: Win10下安装debian共存
date: 2017-03-24 15:34:03
desc:
tags:
---

曾经我也是个倒腾Linux系统安装的人。那时还是学生，对这个命令行像黑客一样的东西特别来劲。通常就花一个晚上到一天不等，就折腾个系统安装。看来有点浪费，不过折腾完系统的安装，现在在Linux上装啥都不怕了（●>∀<●）。循环依赖、源、网络配置、无人值守什么的都算是入了个门。不过自从用了Mac后，距离操作系统更远了，所以就没有怎么搞过。加上Linux本身又比较折腾，有时候不方便也没有那么空闲想来搞一搞。

最近某天，逛网的时候发现[树莓派入手指南](http://droidyue.com/blog/2016/08/22/before-you-buy-raspberry-pi/)，心里很是激动。又唤醒起学生时的兴奋，忽然间燃起来要搞它一搞=≡Σ((( つ•̀ω•́)つ。树莓派不贵，而且感觉比阿里云那些自定义程度更高了。于是就开始在网上搜教程，看了一圈发现技能可能生疏了，得重新拿回。装系统感觉像是一切的开端，那就搞吧。

于是在某天下班后速速回家装机。以前玩的时候还是笔记本、Win7加Linux各种发行版。但是现在Win10有secure boot、BIOS又有UEFI之类的东西，对安装Linux就是各种拦截哇。差点就连安装盘都进不去。满心欢喜装完，结果发现开不了机、卡在初始化后，看来不强行搞一番还对不起装机小能手的称号。

以下是记录时刻。太久没折腾，感觉好多都忘了。虽然装系统的机会不多了，但作为学生时代的情怀还是记录一发好了。

<!--more-->

|---分割线---|
|:-:|
||

背景
-----
目前在dell 8900上装有win10，ubuntu安装失败，似乎安装时识别不了硬盘。
debian是自己曾经用过的。也想过装gentoo，太折腾搞不定，Fedora的话也挺好，不过就是不想装。

前置准备
-----
1. 抗secure boot
> 参考：http://www.jianshu.com/p/2eebd6ad284d

    * 开机时修改secure boot设置
		* F2进入BIOS设置，关闭secure boot
		* F12进入一次性启动选项，配置EFT ON，secure boot OFF的方式
    * win10需要先腾出空的磁盘
    * 禁用快速启动：`win + x` -> 电源选项 -> 选择电源按钮功能 -> 更改当前不可用的设置 -> 去掉“启用快速启动(推荐)”的勾选
2. 刻录U盘
    * 下载debian镜像
    	* 官方源也可以，不过小水管不够快。
    	* 清华源：https://mirrors.tuna.tsinghua.edu.cn/debian-cd/current/amd64/iso-cd/debian-8.7.1-amd64-CD-1.iso
    * 使用UltraISO刻录即可
3. 特殊重启
    * 设置 -> 更新与安全 -> 恢复 -> 立即重启 -> 疑难解答 -> 高级选项 -> 启动设置 -> 立即重启
4. F12进入U盘安装环境

装系统
-----
1. 按步就班
    * 配置语言/地区：English/Hong Kong
    * 配置键盘：American English
    * 配置网络：**暂时不配**。虽然后面发现此时不配太坑了，不过Linux就是折腾不惜的，不慌，爱选啥选啥。
    * 配置磁盘
        * 必须选自定义的
        * 必须注意它推荐的配置：给我的/才配置10G，装完才注意到，算了。
```
# 给个自己理想的磁盘空间设计
/     30G，不介意可以更大
swap  8G，跟内存同样大小
/boot 300M，腾多点给多几个内核
/home 剩下多少是多少
```
    * 配置用户
    * ...
2. 重启
    * 等待安装完就可以重启了
    * 然后重启可能失败，也是因为前面任性，暂时不配网络导致该有的包缺失了
    	* 网卡驱动没装
    	* 显卡驱动不正常

填坑
-----
1. CLI方式进入系统
    * 开机进入Debian的GRUB -> e -> 在`quiet`后加上` nomodeset`
    * F10
2. 配置网络
> 参考：https://wiki.debian.org/NetworkConfiguration

    * 修改文件：`/etc/network/interfaces`
```
auto eth0
allow-hotplug eth0
iface eth0 inet dhcp
```
    * 重启网络以启用服务：`service networking restart`
3. 配置镜像源
> 参考：https://wiki.debian.org/SourcesList

    * 修改文件：`/etc/apt/sources.list`
        * 注释掉CD相关的
        * 解注释系统版本相关的
4. 配置WIFI
> 参考1：https://wiki.debian.org/WiFi/HowToUse
> 参考2：https://wiki.debian.org/rtl819x#Debian_8_.22Jessie.22

    * 添加软件源
```
deb http://ftp.de.debian.org/debian jessie main
```
    * 安装无线工具：`apt-get install wireless-tools`
    * 查看无线硬件：`lspci | grep Wireless`
    * 添加软件源
```
deb http://httpredir.debian.org/debian jessie main contrib non-free
```
    * 安装驱动：`apt-get install firmware-realtek`
5. 配置显卡
> 参考1：https://wiki.debian.org/NvidiaGraphicsDrivers#jessie
> 参考2：http://us.download.nvidia.com/XFree86/Linux-x86_64/340.65/README/supportedchips.html

    * 添加软件源，注意与其他版本驱动的软件源区别
```
deb http://httpredir.debian.org/debian jessie main contrib non-free
```
    * 安装驱动：`aptitude -r install linux-headers-$(uname -r|sed 's,[^-]*-[^-]*-,,') nvidia-kernel-dkms`
    * 安装配置程序：`apt-get install nvidia-xconfig`
    * 自动配置：`nvidia-xconfig`
6. 安装桌面
> 参考：https://debian-handbook.info/browse/stable/sect.graphical-desktops.html

    * 安装gnome：`apt-get install gnome`

配置windows引导
-----
1. 在EFI时代，谁引导都没多大关系，因为入口是由EFI控制。windows引导、ubuntu引导似乎都没啥问题。
2. 修改好启动顺序即可

安装必备软件
-----
```
apt-get install vim
apt-get install git
apt-get install sudo
apt-get install ssh # 安装后默认开机启动
```

更多软件源
-----
* https://mirrors.ustc.edu.cn/help/debian-multimedia.html
* https://mirrors.ustc.edu.cn/help/debian-security.html
* https://mirrors.ustc.edu.cn/help/debian.html
