title: 通过Proxifier代理Google Backup and Sync
date: 2018-10-08 18:45:59
desc: 
tags: [分享, 黑科技]
---

[Proxifier](https://www.proxifier.com/) 作为一款网络代理软件，可以针对某一款软件设置特定代理规则。
- Proxies 用来添加代理服务器，可以添加 proxy chains 用来均衡负载。
- Rules 即 设置规则，为某一款软件的或某一网址进行配置，如果手动配置，记得把shadowsocks 之类的代理软件设置为直连，避免代理无限循环。
- DNS 建议选择使用 Resolve hostnames through proxy , 即使用代理服务器的DNS，避免 DNS 污染。

[Backup and Sync from Google](https://www.google.com/drive/download/backup-and-sync/) 是谷歌官方Drive数据同步工具。众所周知的原因，Google 的服务在国内都是屏蔽的。

在国内，即使开了ShadowSocks全局代理，Backup and Sync 依然会报下面的错误信息：
> google photo backup there was a problem to authorization，google drive unable to connect mac

原因是这里Backup and Sync不支持上层的http、sock代理，如果使用需要在 tcp层面进行代理。这时候Proxifier就出派上用场了。

<!-- more -->

## 步骤

1. Proxifier下载安装
    - 官网下载就可以：https://www.proxifier.com/
    - 此处分享几个验证码，仅供测试：注册码不通用，注册用户名任意。 
        ```
        L6Z8A-XY2J4-BTZ3P-ZZ7DF-A2Q9C (Portable Edition)
        5EZ8G-C3WL5-B56YG-SCXM9-6QZAP (Standard Edition)
        P427L-9Y552-5433E-8DSR3-58Z68 (MAC)
        ```
2. 配置规则
    - 添加Backup and Sync的App，并指向本地的ShadowSocks端口
    <img src="{% asset_path rules.png %}" alt="" width="500" height="270" />
    
## 参考
1. 使用 Proxifier, 为Google Drive 的 Backup & Sync 进行代理：https://blog.batesma.com/20170811-google-drive-proxy-solved/
2. 使用 Proxifier 解决 Google photo backup/Google drive mac 无法连接错误：http://www.ixirong.com/2016/11/08/fix-google-photo-problem-by-proxifer-and-shadowsocks/