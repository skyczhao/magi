title: 在Webstorm中写hexo
date: 2018-09-16 21:10:54
desc: 
tags: [] 
---

博客是hexo搞的，感觉还蛮干净清新的。
这里分享下博主写博客的配置。

<img src="{% asset_path 1.png %}" alt="" width="" height="" />
* Editor -> File and Code Templates
```
title: ${title}
date: ${YEAR}-${MONTH}-${DAY} ${HOUR}:${MINUTE}:${SECOND}
desc: ${description}
tags: [${tags}] 
---

<!-- more -->
```

<!-- more -->

主要的配置就是上面这个，对markdown文件的模板。
hexo其实也可以在命令行创建新的博文md，但是总是不太舒服。该短横线的会换成下划线，title也总是要自己建立了md之后手动改。不像英文，文件名和标题名保持一致就好了。
使用时注意新建博文时采用markdown的模板。会弹出个框，让你填好内容，生成文件。
<img src="{% asset_path 2.png %}" alt="" width="460" height="220" />

然后就是用`nvm`管理node的版本的。也是hexo推荐的方式。