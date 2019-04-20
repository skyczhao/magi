title: 代理小工具
date: 2019-03-28 22:30:29
desc: 
tags: [分享] 
---

外包做得多了，常常被客户内网环境恶心到。为了保护内网信息，许多公司都会引入VPN，各种各样都有。

常见的方式，就是让你账号密码登陆一遍VPN就可以。这种也是最理想的，但是有些不一定适用于Mac。常见的例如SSL VPN、OpenVPN、Cisco VPN...

有些恶心的就会加上动态密码，输入密码同时还需要添加一小段token到密码后面。常见的就有VIP Access、RSA SecurID、短信验证码...

<img src="{% asset_path 1.jpg %}" alt="TOKEN" />

再恶心点，就是登陆VPN后，也只能访问指定的页面。如果要登陆终端，会弹出下载内容，用xshell或者rdp打开，一般有效期都比较短。

<img src="{% asset_path 2.jpg %}" alt="WEB CONTROL" />

为了提高工作效率，一些Proxy的小技巧就不得不用上。虽然理解客户出于保护数据的做法，但毕竟工序太长，耗太多时间在链接上就不太好。

<!-- more -->

两个明星工具：
* Fiddler
* Proxifier

#### Fiddler
> [Fiddler - Free Web Debugging Proxy - Telerik](https://www.telerik.com/fiddler)

Fiddler实际上是个抓包工具。但是有没有想过其实它也是一个极佳的代理工具？
在Windows上，如果你想打造成一个代理服务器，你会怎么做？会不会忽然间就脑子短路，没想到用啥？还是说nginx？apache？
其他的工具，个人看来都没有Fiddler方便。

它的优势：
* 免费、轻量
* 易用：打开就是HTTP代理，使用时在Wifi配置、SwitchyOmega等都轻松配置，在终端`export HTTP_PROXY...`就轻松加入代理
* 可视化：能够轻易看到经过代理的有什么流量

<img src="{% asset_path 3.png %}" alt="Fiddler" />

#### Proxifier
> [Proxifier - The Most Advanced Proxy Client](https://www.proxifier.com/)

看其官网标题就可以感受到它的自信。
其强悍的定点代理能力是我感觉特别棒的地方。举个例子，有个代理就只是发生在某几个APP上，你会怎么办？例如Google的同步服务，其他的都不会一直挂着FQ吧？
而且其支持的协议层级更低，HTTP代理层无法骗过APP的都可尝试用一下它。

<img src="{% asset_path 4.png %}" alt="Proxifier" />

不知道这两个工具你用过没有？
