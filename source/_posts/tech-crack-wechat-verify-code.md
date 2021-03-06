title: 破解微信验证码
date: 2018-05-05 16:53:20
desc: 
tags: [安全] 
---

这个小功能其实在3个月前已经做了。一直没发出来，一方面是因为属于公司的代码；另一方面担心有不良影响。不过现在想想，感觉应该一种技术交流的理念去看待这件事。

验证码本质是认证方式的一种。在计算机安全领域，3A包括认证、授权和会计(Authentication, Authorization and Accounting)。认证(Authentication)即证明是什么。通过验证码，系统用作识别操作的对象是否是真人(图灵测试，Turing Test)。

这里只涉及对特定场景下图片验证码的识别跟通过。验证码其实有很多种类，常见的例如文字验证码、数值与数值计算验证码、图像验证码等。这里处理是在微信注册时出现的图像验证码。该分享本意是交流图像的处理方法。

至于为什么拿这个作为研究，一方面微信这个APP常用啊，说出来应该有不少人有共鸣；另一方面素材容易获取、场景也简单，虽然后来微信对此有所加强，不过总的来说还是很适合拿来练手的。

不多说，下面进入正文。

<!-- more -->

## 场景

微信注册登录过程中，有一个简单的图片验证码模块。注册者需要手动滑动验证码上的滑块，到图中指定位置才能通过并进入下一步。

<img src="{% asset_path 1.png %}" alt="" width="276" height="491" />

破解目标：找到滑块的终点位置。因为起点是固定的。

## 1. 裁剪

<img src="{% asset_path 2.png %}" alt="" width="" height="" />

## 2. 初步定位

<img src="{% asset_path 3.png %}" alt="" width="" height="" />

## 3. 缩小范围

<img src="{% asset_path 4.png %}" alt="" width="" height="" />

## 4. 最终位置

<img src="{% asset_path 5.png %}" alt="" width="" height="" />