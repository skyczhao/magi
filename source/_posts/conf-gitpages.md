title: "常见gitpages设置"
date: 2015-10-31 14:24:00
tags: 配置
---

## [github](https://github.com/)
1. 创建一个以用户名命名的仓库
	* 新建仓库，属性为public，命名：[username].github.io，例如：skyczhao.github.io
	* 这个仓库master分支会被直接用于页面显示
	* 创建完仓库后，github会自动分配一个子域名：[username].github.io，目前来说还是不可以访问的
2. 往仓库master分支添加页面
	* 添加一个index.html的页面
		- 此处为了测试，写了个简单的页面
		- 文件名：index.html
		- 源码：`<h1>Hello, [username]!</h1>`
3. 打开主页
	* [username].github.io

### 绑定域名(可选项)
1. 先要有个域名
	* 可以在[godaddy](https://www.godaddy.com)、[万网](http://wanwang.aliyun.com/)等地方注册
	* 个人建议不要找小型网站注册
2. 添加域名解析
	* 进入到域名解析修改的页面
	* 添加CNAME解析(如果是直接用IP地址，就改成A解析)
	* 主机记录为www或者@，分别对应带www以及不带www的域名(以www.example.com为例，www对应的是www.example.com，@对应的是example.com)
	* 记录值填刚刚的地址：[username].github.io

	
## [gitcafe](https://gitcafe.com/)
1. 创建一个以用户名命名的仓库
	* 直接以用户名为命名即可，命名：[username]
	* 与github不同，这里用于显示的分支为：gitcafe-pages
	* 分配的子域名为：[username].gitcafe.io
2. 往仓库的gitcafe-pages分支添加页面
3. 打开主页
	* [username].gitcafe.io

### 绑定域名(可选项)
1. 大体设置差不多
2. 记录值改为：gitcafe.io

## [coding](https://coding.net)
1. 创建一个以用户名命名的仓库
2. 往默认的`coding-pages`分支添加index.html
3. 在项目设置中，找到pages，启动服务
4. 打开主页
	* [username].coding.me
