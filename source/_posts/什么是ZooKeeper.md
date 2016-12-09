---
title: 什么是ZooKeeper
date: 2016-12-09 18:56:53
tags:
  - 自学
  - Zookeeper
---
由于工作需要，最近两天要了解了一下Apache的Zookeeper。趁着新学的热乎劲，想以初学者的角度基本介绍一下它们是什么，为什么需要他们，以及他们干什么的。至于怎么干的，如何配置等等这种深入的东西，我是不会说的。因为暂时还不知道。

## 什么是ZooKeeper
[官方介绍英文版](https://zookeeper.apache.org/doc/trunk/zookeeperOver.html)

>ZooKeeper is a distributed, open-source coordination service for distributed applications. It exposes a simple set of primitives that distributed applications can build upon to implement higher level services for synchronization, configuration maintenance, and groups and naming.

>Zookeeper是针对大型分布式系统的高可靠的协调系统。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、命名服务、分布式同步、组服务等。

初学的看到这几句话头都大了。这种解释言简意赅，就跟数学定义是的，背下来还行，至于说用来了解它到底是什么，还是算了吧。好，下面开始说人话。

<!--more-->

从上面的说法，我们可以知道，ZooKeeper是个**协调系统**，目标**服务对象**是大型**分布式**系统, 提供的服务是**一致性**服务。一般一说到**分布式**，就会涉及很多个计算机一起合作工作，合作就需要互相沟通协调，而ZooKeeper就是用来提供这个沟通协调服务的。服务内容跟**一致性**有关，一致性就是一起合作的计算机在信息上保持一致。比如说上面提到的_配置服务_, 要保证素有计算机都有统一的系统配置。既然都是分布式系统了，协调工作什么的自己弄不就行了么，为什么需要ZooKeeper呢？

## 为什么需要ZooKeeper
因为简单，可靠，快捷。分布式系统的协调工作是出了名的难搞，一不小心就出错，而且还不好调试。而且分布式系统的本意是**使用**分布式, 是想用分布式提供的好处优点，而不是**实现**分布式本身。像这种琐碎，难搞，还容易出错的部分，当然是用现成的服务好了。而ZooKeeper恰恰就是为此而生的，简单，可靠，快捷。毕竟是Apache名门之后。

## ZooKeeper特点

### 分布式
值得一提的是ZooKeeper本身也是**分布式**的。

![zkservice](/images/zookeeper/zkservice.jpg)

如上图所示， 上面的ZooKeeper Server(下面会简称位 _ZkServer_ )构成了ZooKeeper服务，下面的 _Client_ 就是服务的对象(某个分布式系统应用)。ZooKeeper做成分布式的原因当然也是冲着分布式的优点了，高性能，高可用性。说白了就是 _ZkServer_ 多了力量大，而且不怕某个或某几个 _ZkServer_ 挂掉，只要大多数 _ZkServer_ 还在，照样提供服务。每个_Client_都是只连到单个的 _ZkServer_，如果这个连接断掉了，_Client_ 会被连到其他 _ZkServer_。在 _ZkServer_ 集群被启动的时候，会统一推举一位Leader,对应地其他 _ZkSever_ 就会成为Follower。_Client_ 写数据的请求会交给Leader同意处理然后广播给每个Follower，以保证数据的**顺序一致性**(这是ZooKeeper的一个特性，保证所有数据的更新顺序和他们被发送的顺序一致)。就是需要一个Leader主持一下，不然所有 _ZkServer_ 分别接到写数据的请求，就没法知道谁先谁后了。最重要的是，如果Leader挂了，大家都会知道，然后会毫不犹豫地选举一位新Leader。

### Data Model
ZooKeeper每个 _ZkServer_ 都会保存一份完整的数据(主要是用于协调_Client_的数据，状态信息，配置信息，位置信息等等)，这样才能保证每个_Client_都能得到完整的信息以相互合作。ZooKeeper采用了类似文件系统的数据结构来存储以上数据，如下图所示。

![zknamespace](/images/zookeeper/zknamespace.jpg)

注意上图是每个 _ZkServer_ 中保存的数据结构，每个节点叫做 _ZNode_。之前提到的各种信息就会存在对应的 _ZNode_ 中。不用于文件系统的数据结构(存储在硬盘中)，这些信息占用空间小，说以会被直接存到**Memory**以实现高性能，低延迟。_Client_ 读写的就是这些 _ZNode_ 里面的数据，也可以增加或者删除相应的 _ZNode_。_Client_ 还可以使用Watch监视某些 _ZNode_ 如果对应的数据有变化， _Client_ 会被通知到。

### 特性总结
> 顺序一致性：_Client_ 的更新顺序与更新被发送的顺序一致

> 原子性：更新操作要么全部成功，要么全部失败

> 单一系统视图：无论_Client_ 连接到哪一个_ZkServer_，都可以看到相同的_ZNode_结构视图

> 可靠性：一旦一个更新操作被应用，那么在_Client_ 再次更新它之前，其值将不会被改变

> 及时性：在特定的一段时间内，系统的任何变更都将被_Client_ 检测到
