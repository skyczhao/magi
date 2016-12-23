title: 在ElasticSearch节点之间“搬动”shard
date: 2016-06-13 09:00:17
tags: ElasticSearch
---

背景
-----
现在有两台ES节点，已经配置好成一个cluster。在测ES的时候，因为改动了源码，需要更换ES的和新jar包，并重启。  
为充分测试，更好地模拟线上生产环境，索引以及类型的设置都按照一定规范。  
但是当节点重启后，发现索引的shard都集中在一个节点103上，其余都是`unassigned`的状态，而且集群状态为`yellow`。
<img src="{% asset_path 0.png %}" alt="" />
于是就有了“搬动”的需求。

<!--more-->

“搬动”
-----
### 允许自动分配
先测试自动allocation是否成功

```
curl -XPUT 'localhost:9200/_settings' -d '{
    "index.routing.allocation.disable_allocation": false
}'
```

### 按大小分配的配置
因为在尝试搬动的过程中，ES返回的错误信息包含了一条前置条件检测不通过的信息。大概意思是容量超过的85%，导致无法搬动shard，具体忘了截图。
所以有两条方法，1. 关掉这个限制，2. 提高原来关于容量占用的设置百分比。

> 其实做完这个后，ES已经自动把shard在节点之间进行balance。。。但是生命不息、折腾不止。。。

* 关闭

```
curl -XPUT localhost:9200/_cluster/settings -d '{
    "transient" : {
        "cluster.routing.allocation.disk.threshold_enabled" : false
    }
}'
```

* 修改设置
> 后来经过验证，是104的磁盘占用率超过了85%。。。

```
curl -XPUT localhost:9200/_cluster/settings -d '{
    "transient" : {
        "cluster.routing.allocation.disk.watermark.low" : "90%",
        "cluster.routing.allocation.disk.watermark.high" : "50gb",
        "cluster.info.update.interval" : "1m"
    }
}'
```

### unassigned处理
> 参考：[ElasticSearch: Unassigned Shards, how to fix?](http://stackoverflow.com/questions/19967472/elasticsearch-unassigned-shards-how-to-fix)

* 强制allocate shards，可能导致数据丢失，“搬动”

```
curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
    "commands": [{
        "allocate": {
            "index": "hwsearch",
            "shard": 1,
            "node": "node104",
            "allow_primary": 0
        }
    }]
}'
```

* 允许自动allocation？

```
curl -XPUT http://localhost:9200/_cluster/settings -d '
{
    "transient" : {
        "cluster.routing.allocation.enable": true
    }
}'
```

### 手动实现主副shard的切换
> cancel primary的shard，主动allocate新的primary。。

```
curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
  "commands": [
    {
      "cancel": {
        "index": "hwsearch",
        "shard": 2,
        "node": "node104",
        "allow_primary": 1
      }
    }
  ]
}'
```

<img src="{% asset_path 1.png %}" alt="" />
=>
<img src="{% asset_path 2.png %}" alt="" />

常用的操作
-----
### 查看shards的状态

`curl -s "http://localhost:9200/_cat/shards"`

### restful api关单独的node
`curl -XPOST 'http://localhost:9200/_cluster/nodes/_local/_shutdown'`

### 改index的副本数

```
curl -XPUT 'http://localhost:9200/hwsearch/_settings' -d '
{
    "index" : {
        "number_of_replicas" : 1
     }
}'
```

由0到1可以实现primary的切换。。

replica与primary shard的解析
-----
> Ref：[replica shards](https://www.elastic.co/guide/en/elasticsearch/guide/current/replica-shards.html)