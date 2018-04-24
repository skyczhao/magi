title: 隧道的几个方法
date: 2018-04-22 12:20:35
desc: 
tags: [分享,技术] 
---

相信大家在遇到限制的网络都有种无奈和不爽吧。。举两个场景：客户提供访问对方内网的VPN，而访问正式服务器还需要经过一层跳板机；某些站点资源绑定了访问的出口IP，一旦离开指定网络就无法访问...像这些限制就是大大降低程序员的生产效率哇。

但有防御就有攻击。既然对方都摆出这么些阵势，那我们自己也需要备一些政策应对。

这里就大概分享下博主用到的几种方式。实用为主、简单为主，但是能解决问题就是极好的。

<!-- more -->

## ssh (Linux/Mac)

ssh是一个很强悍的工具好吧。
* 如果没有顺手的集群运维工具，如saltstack之类的，ssh（可能）就是远程执行命令的唯一选择了。
* 同时，ssh连接到主机后，该连接自身就可以作为一条服务器与客户机的通道。

以下的命令就是利用ssh登陆主机后形成的链接来作文章，形成隧道以供多种用途：
1. 本地端口转发
    命令：`ssh -L 2121:host2:21 host3`
    核心参数：`-L`
    登陆位置：localhost
    应用场景：<img src="{% asset_path share-tm-02.png %}" alt="" width="250" height="90" />
2. 远程端口转发
    命令：`ssh -R 2121:host2:21 host1`
    核心参数：`-R`
    登陆位置：host3
    应用场景：<img src="{% asset_path share-tm-03.png %}" alt="" width="250" height="90" />
3. 绑定本地端口
    命令：`ssh -qTfnN -D 1080 dev@host3 -p 3822`
    核心参数：`-D`
    登陆位置：localhost
    应用场景：<img src="{% asset_path share-tm-04.png %}" alt="" width="250" height="90" />
    
## netsh (Windows)

虽然大多数服务器都是Linux的系统，但作为跳板机可能是Windows的。
Windows上也存在类似端口转发的工具，也就是`netsh`。

命令：打开Windows的cmd
1. `netsh interface portproxy show all`
2. `netsh interface portproxy add v4tov4 listenport=51433 connectaddress=host2 connectport=1433`

执行位置：host3
辅助配置：<img src="{% asset_path share-tm-06.png %}" alt="" width="600" height="90" />
应用场景：<img src="{% asset_path share-tm-05.png %}" alt="" width="250" height="90" />

## xshell (Windows)

> Windows没有ssh的命令，但是ssh的工具一把。我就把我常用并且感觉用起来最便利的一款推荐。

纯粹GUI配置：
1. 常规配置：登陆的host以及账号密码
<img src="{% asset_path share-tm-07.png %}" alt="" width="285" height="85" />
2. 隧道配置：切换到隧道页面，选择隧道类型并填写对应参数
<img src="{% asset_path share-tm-08.png %}" alt="" width="285" height="130" />
    
## 辅助工具

1. Proxy SwitchyOmega （[Chrome Web Store](https://chrome.google.com/webstore/detail/padekgcemlokbadohgkifijomclgjgif) / [CRX](https://github.com/FelisCatus/SwitchyOmega/releases)）
    参考配置：<img src="{% asset_path share-tm-01.png %}" alt="" width="420" height="85" />
    
    
## 参考文献
1. [SSH原理与运用（二）：远程操作与端口转发](http://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html)
2. [在windows上用netsh动态配置端口转发](http://aofengblog.blog.163.com/blog/static/631702120148573851740/)