title: "博客搭建"
date: 2015-04-15 14:57:08
tags: 配置文档
---

### 安装
1. git
    `sudo apt-get install git`
2. Node.js
    ```
    # 下载安装nvm
    wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh 
    nvm ls-remote    # 先查看远程仓库包含的版本信息
    nvm install 0.12 # 选择其中一个版本安装
    nvm use 0.12     # 使用安装的版本
    ```
    注意：
    * nvm需要正确添加到执行路径才能在命令行直接运行（一般安装时默认添加到`.bashrc`里面了，如果有需要可以添加到例如`.zshrc`里面）。
    * nvm每次启动时，并没有指定默认加载哪个Node.js版本，所以可能每次都需要执行`nvm use 0.12`
3. hexo
    `npm install -g hexo-cli`

### 配置
1. 创建一个博客目录
    ```
    # 先进入一个你想放博客的目录
    hexo init mysite # 初始化一个博客目录
    cd mysite        # 进入博客目录
    npm install      # 配置Node.js的server
    ```
    注意：
    * hexo创建博客目录的时候，自带一个`.gitignore`文件，可以自行查看忽略了哪些内容
    * 当使用Git管理的时候，会把`npm install`产生的配置目录忽略的，所以在下次`git clone`完后，想运行的话需要重新运行一次`npm install`。
2. 修改配置
    编辑文件`_config.yml`
    * 基本配置
        - Site
        - URL
    * 主题配置
        1. 先从[themes](http://hexo.io/themes)找到所需要的主题，例如：phase
        2. 找到其Github的地址，clone到本地themes目录下
        `git clone git://github.com/tommy351/hexo-theme-phase.git themes/phase`
        主题的Github页面下也有说明
        3. 修改：Extensions->theme
        `theme: phase`
    * 部署配置
        - Deployment
        ```
        deploy:
          type: git
          repo: [你的Git仓库地址]
          branch: gitcafe-pages [可以用来显示pages的分支，这里是gitcafe的设置]
          message: Site updated:{{ now("YYYY-MM-DD HH:mm:ss") }}
        ```
        - 要使用自动部署，还需要安装一个工具
        `npm install hexo-deployer-git –save`
    * 添加Disqus评论
        1. [Disqus](https://disqus.com)
            - 注册
            - `Setting -> Add Disqus To Site`，填写好相关的资料，期间就能看到`shortname`了
            - 完成后，选`Universal Code`
            - 然而并没有什么卵用（什么！我竟然说这种话？），因为`hexo`已经很好地集成了`Disqus`，所以只需要`shortname`即可
        2. 直接加到配置文件里面
        ```
        # Disqus
        disqus_shortname: [在disqus上申请的shortname]
        ```

#### 常用命令
1. 启动本地服务器：`hexo server`
2. 新建POST：`hexo new "My New Post"`
3. 生成和部署
    * 生成静态文件：`hexo generate`
    * 部署到远程仓库：`hexo deploy`
    * 生成并部署：
        `hexo generate --deploy`
4. 清理：`hexo clean`，有小问题时可以这样处理一下看看

### 参考文档
* [hexo 官方文档](http://hexo.io/docs/)
