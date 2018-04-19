title: 深入探究ElasticSearch节点管理
date: 2018-04-19 21:54:49
desc: 
tags: [分布式] 
---

ElasticSearch其实是一个很棒的分布式学习案例。尚记得大约两年前在项目中对ES进行源码的阅读以及修改，也算是第一次阅读优秀的开源项目。当时就被ES精巧的实现折服。或许是第一次阅读好代码，毕竟当时刚毕业，还没真正写过几个项目就开始这个项目。所以也是打开了自己对系统、编程的新的理解大门。在当时已经是比较热门的分布式搜索引擎，经过两年后热度有增无减，确实是比较难得的。

当时比较令我惊讶的是"无master"的方式。也就是每个节点都可以接受查询请求。这个为什么觉得惊讶，是因为感觉到了这样的结构，应该是没有边界的。也就是只要有机器，部署一套ES就能索引无限的数据，而且结构足够简单。可能说得过大了，但当时给我的就是这么惊讶。对比Hive、HBase这些类似的，都是需要一个master，要么是HiveServer2，要么是HMaster。个人愚见，如果存在master的分布式结构，master都可能是一个瓶颈，而这样的分布式不够完备。而ES却不是。

不过ES后来也发展出master，但没看懂是什么。上一次阅读源码，针对的是[ES 1.7.1](https://www.elastic.co/blog/elasticsearch-1-7-1-and-1-6-2-released)。而现在的最新版本到了[ES 6.2.4](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/release-notes-6.2.4.html)，中间已经不知道发生了多少变化。在互联网行业，2年足以改变许多，小米都经历了过山车式的发展对不？ES的发展其实也蛮值得学习的，一开始虽然工作看似普通（在lucene基础上增加分布式的支持），后来发展衍生出一整套解决方案（例如ELK）。对开发来说、对做产品来说都是一个很好的启发。

而接下来先会对新版的节点管理进行学习。我还会持续关注ES以及对应的演进，其他相关的我也在学习中。

<!-- more -->

## ES节点综述
* ES正常运行时只能有一个master(即leader)，多于1个时会发生脑裂。
* 数据节点会存储分配在该node上的shard的数据并负责这些shard的写入、查询等。
* 任何一个集群内的node都可以执行任何请求，其会负责将请求转发给对应的node进行处理。

## 节点管理
#### ES集群构成

<img src="{% asset_path es-nm-01.jpg %}" alt="" width="400" height="350" /> 

Node可以有不同的类型，通过以下配置，可以产生四种不同类型的Node：

```yaml
conf/elasticsearch.yml:
    node.master: true/false
    node.data: true/false
```

#### 节点发现

> ZenDiscovery: 是ES自己实现的一套用于节点发现和选主等功能的模块，没有依赖Zookeeper等工具

节点到集群中的每个节点建立一条边，当整个集群所有的node形成一个联通图时，所有节点都可以知道集群中有哪些节点，不会形成孤岛。

节点与节点间互相发现或确认的方式包括两种：
* Ping：节点通过发现机制找寻其他节点
* Unicast：单播发现需要一组hosts作为路由。官方推荐这一组hosts都应该配置成master-eligible节点。
```yaml
conf/elasticsearch.yml:
    discovery.zen.ping.unicast.hosts: [1.1.1.1, 1.1.1.2, 1.1.1.3]
```

#### Master选举

集群中可能会有多个master-eligible node，此时就要进行master选举，保证只有一个当选master。如果有多个node当选为master，则集群会出现脑裂，脑裂会破坏数据的一致性，导致集群行为不可控，产生各种非预期的影响。

选举过程：
* **发起**：由master-eligible节点发起。
* **选举谁**
    - 先按clusterStateVersion进行排序：当clusterStateVersion越大，优先级越高；Master当选后，就会以这个版本的clusterState为基础进行更新；
    - 再按节点的Id进行排序：节点的Id越小，优先级越高；为了让选举结果尽可能稳定，不要出现都想当master而选不出来的情况；
* **选举结束**：**TBA**
* **保证不脑裂**：目前的ES 6.2还存在问题。master候选人需要等待多数派节点进行join后才能真正成为master，就是为了保证这个master得到了多数派的认可。但在选举过程中，一个Node可能不仅仅投一票。

## 错误监测

1. MasterFaultDetection与NodesFaultDetection
类似心跳的机制，有两类错误检测：一类是Master定期检测集群内其他的Node；另一类是集群内其他的Node定期检测当前集群的Master。
    
2. rejoin
Master发现自己已经不满足多数派条件(>=minimumMasterNodes)了，需要主动退出master状态(退出master状态并执行rejoin)，以避免脑裂的发生。

## 集群扩缩容

* 数据节点扩容
* 数据节点缩容
* Master节点扩容
* Master节点缩容

## 参考文档
* [Elasticsearch分布式一致性原理剖析(一)-节点篇](https://zhuanlan.zhihu.com/p/34858035)
* [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html)