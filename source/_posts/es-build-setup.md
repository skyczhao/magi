title: ElasticSearch源码开发环境配置
date: 2016-06-26 21:01:56
tags: [ElasticSearch,搜索引擎]
---

背景
-----
由于项目需求，最近对ElasticSearch（以下简称ES）源码进行阅读分析，并进行了改造，以适应客户要求。
大家都知道ES是一个开源的项目，而且它在Github上托管了源码。所以源码可以很容易获得。

<!--more-->

配置
-----
### 源码
* version：1.7.1
* size：21M
* desc：也是项目的原因，这里用的ES版本是1.7.1。从Github上的tag版本中选择1.7.1，然后使用clone或者zip的方式下载源码即可。

### 打开
* IDE：IntelliJ IDEA 15
* desc：IDEA据说是最好的Java IDE。

### 依赖配置
像流行的方式一样，ES也采用了maven对各种依赖关系进行管理。而且IDEA对maven项目的支持还是蛮好的，打开ES项目后，选择允许自动下载依赖，IDEA就会调用maven下载依赖的jar。

### 运行
* main class：`org.elasticsearch.bootstrap.Bootstrap`
* options：`-Des.foreground=yes` （前台显示日志）
* desc：如下图，打开运行配置窗口后，填写如下配置。从中可以发现ES是通过Bootstrap进行启动的，后面随着源码阅读的深入，会发现ES另一个重要的运行工具：通过Guice进行注入。

<img src="{% asset_path 1.png %}" alt="运行参数配置" />

* 配置完后直接运行，访问`http://localhost:9200/`看一下输出就可以确认ES启动成功。

### 插件配置

#### head
裸的ES，要么通过API（如Java API）执行访问，要么通过RestfulAPI进行访问，但是都显得没那么人性化。所以这里会选择装上head插件，一方面方便看资源状况，另一方面方便访问（在窗口改query body明显舒服很多）.
安装方式跟正式的ES node差不多，同样在Github上下好对应版本的head插件，解压到ES项目下的`plugins`目录即可，然后通过`http://localhost:9200/_plugin/head/`进行访问。
为了方便编辑和查看json结果，我上网找了一圈好用的插件或者应用，发现直接一个web app就足够了，可以参考一下`http://codebeautify.org/jsonviewer`

#### IK
相信在处理汉语的时候，分词是个重要的问题。这里会直接选用流行的IK分词进行使用，其也支持自定义词库。不过这里使用的主要目的是为了测试而模拟真实的线上环境，以用于开发调试。所以没太考虑效果，有就行。
配置的方式：

1. 选择合适版本，ik 1.2.6 -> es1.7.1，也可以在Github上下载得到
2. 可以自己打包，配置都在ik项目的`config`目录下，包括配置和词库，不用上网找，直接拿来使用
3. 跟head插件的方式类似，同样放在`plugins`目录下，对应增加`elasticsearch.yml`的配置即可
4. 坑：想要测试的话，这个版本的ik分词要求先有索引，指定调用ik分词字段，然后才会加载使用，README上说的内容稍显过时。
5. 参考测试：`http://localhost:9200/your_index/_analyze?analyzer=ik&pretty=true&text=%E6%88%91%E6%98%AF%E4%B8%AD%E5%9B%BD%E4%BA%BA`