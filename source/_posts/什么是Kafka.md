---
title: 什么是Kafka
date: 2016-12-12 22:22:59
tags:
  - Kafka
  - 自学
---
跟上一篇[什么是ZooKeeper](https://blog.ubyte.me/p/70e69dfb/)一样，只是简单记录一下初学心得，没有很深入的东西，但应该足够让人知道什么Kafka。

## 什么是Kafka
先上[官方介绍](https://kafka.apache.org/intro)。
> Kafka™ is a distributed streaming platform.
>
> Kafka 是一个分布式的消息系统。

从简短的定义可知，Kafka是**分布式**的，它是个消息系统，或者按着英文原意，是个流系统(Stream Platform)。其实，Kafka既是一个**消息系统**，也是一个**流系统**，还是一个**存储系统**。


Kafka 擅长的领域：
1. Building real-time streaming data pipelines that reliably get data between systems or applications
2. Building real-time streaming applications that transform or react to the streams of data

概念:
1. Kafka运行在集群上(一个或者多个Server)
2. Kafka集群以不同类别存储记录流，类别标记位topics
3. 每个记录由Key,Value,和Timestamp组成

API:
1. Producer: 用来向一个或多个topic发布记录流
2. Consumer: 用来订阅一个或多个topic，并且处理产生的记录流
3. Streams Processor: 用来消耗(Consume)一个或多个topic的输入流，处理之后转化成一个或多个topic的输出流
4. Connector: 用来创建并运行可重用的Producer或者Consumer来连接Kafka和已有的应用或数据系统。

Topics and Logs:
	Partition: 一个topic一般会有多个订阅者，每个topic会被分成partition。1. 不会被Server的容量限制，虽然每个Partition会被Server容量限制，但是每个Topic可以有多个Partition。2. 为了性能，Partition之间是并行的。
	记录存留： Kafka会在可设置的时间范围内保存所有记录(不管记录有没有被消耗，一般来说传统消息队列Message Queue的记录一旦被消耗就会删除)。Kafka性能不会被数据大小影响，存留数据不会影响性能，以时间复杂度为O(1)的方式提供消息持久化能力。
	灵活的Consumer: 每个Consumer自己控制Offset选择记录的读取，不会影响其他的Consumer。

分布式: Partition是分布在集群上的，每个Server只负责一部分Partition。为了容错，每个Partion会被复制到一定数量的Server上，以Lear-Followers的模式存在。一个Partition的Leader可能是另一个Patition的Follower，平衡负载。

Producer: Producer决定消息发布Topic的哪一个Partition。可以用轮流发布，或者其他的方式平衡负载。

Consumer:
	Consumer会分成Consumer Group。被订阅的消息只会传递给Consumer Group中的一个Consumer； 如果有多个Consumer，消息的发送会被平衡负载给每个Consumer。Consumer可以在不同的进程甚至不能的机器上。
	可扩展性和容错性： 其实这样就是一个Pub-Sub(发布订阅)模式，只不过订阅者是一个集群而不是一个单独的进程。这样可以提高可扩展性和容错性。
        平衡负载： Kafka会把Topic的Partition平均分给Consumer Group的每个Consumer，并且负责动态地管理Consumer Group，如果Consumer成员变化(成员加入或者挂掉)，Partition会被重新分配。

        Total Order Within Partition: 记录只有在Partition内是全序(totoal order)的，就是可比较的。不同Partition里的记录不可比较。一般来说，根据不同Key分Partion就满足大部分应用了(就是不同key的记录不需要比较)。如果实在想要Topic的全序，就只能Topic单个Partion了。

Kafka可以保证，同一个Producer向同一个Partion发送的消息顺序性，并且Consumer读到记录的顺序就是在Partition存储的顺序；并且具有“光杆司令”的容错，复制因子N，容忍N-1个Server挂掉。


Kafka作为消息系统：
	传统的消息系统分两种模式：
		1. 队列模式： 平衡分配任务给多个Consumer扩展处理；但是不能发送给多个Consumer。
		2. Pub/Sub发布订阅模式： 可一个广播给每个订阅者； 但是不能扩展处理。
	Kafka归纳了两种模式，对于不同的Consumer Group，是Pub/Sub发布订阅模式; 对于Consumer Group中的每个Consumer，是队列模式。
	传统的队列模式只能保证消息在Server的顺序，然而由于消息传递到不同Consumer是异步的，就不能保证到每个Consumer的顺序。Kafka做的比较好，利用Partion和Consumer Group， 使一个Topic的每个Partition分给Group里的每个Consumer并保证每个只有一个Consumer在处理相应的Partition，既保证了顺序又平衡了负载。然而，一个Consumer Group里的Consumer数量不能多余Partition数量。

Kafka作为存储系统：
	Kafka把记录存储到硬盘并复制保证容错，并用写操作确认来保证producer发布的记录被完全复制完毕。Kafka性能不收数据大小影响，即使对TB级以上数据也能保证常数时间复杂度的访问性能，并且允许Consumer控制Offset读取记录。综上所述，Kafka可以当做高性能，低延迟的分布式文件系统。

Kafka的真正作用是用来做即时数据流处理。

消息，存储，数据流，三合一，看起来不寻常，但实则是必须。一般的文件系统如HDFS只能存储静态文件批量处理历史数据，或者消息队列只能处理订阅的消息。而Kafka通过这三项的有效组合，既可以处理历史数据(离线处理)，又可以处理未来消息(实时处理)。
1. 对于数据流应用，通过存储功能和低延迟的消息订阅，数据流应用可以用同样的方式处理历史数据和未来数据。
2. 对于数据流管道，订阅实时时间使得Kafka可以被用来当做低延迟管道；存储功能保证数据的完整不丢失；数据流功能使得管道可以转换数据。
