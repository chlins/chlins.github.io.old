---
title: Paxos
date: 2017-10-18 16:21:40
tags: 分布式
---

#### Paxos算法初识

在分布式领域中，许多共识算法都是由这个算法演化而来或者说是简化版的实现，如zookeeper使用的zab以及etcd使用的raft等。

在分布式中，算法用来保证状态机复制的一致性，也就是说保证所有节点中数据是一致的。Paxos算法中主要包含四种角色：

* Client：由client提出议题，类似广大民众
* Proposer：根据client提出的议题，向上提议，类似人大代表
* Acceptor：决策者，类似最后的投票表决者
* Learner：执行者，当某个议题通过时，它将采取行动
* Leader：领导者，只会存在一个领导者

执行阶段：

1. Prepare：Proposer准备一个编号为N的议题，通知Acceptor多数者讨论。
2. Promise：Acceptor回应Proposer同意。
3. Accept Request：如果多数Acceptor同意，Proposer会首先进行set操作，并通知Acceptor。
4. Accepted：Acceptor接收，此后Learner会执行。

不过Basic Paxos算法过于繁琐复杂，工程上更多采用的是Multi Paxos即简化的Paxos，如raft或zab，它们与Paxos相比，角色更少，一般只有三种角色状态，因此应用更加广泛。

附wiki上的一张图，非常直观的显示了Paxos的流程：

```
Client   Proposer      Acceptor     Learner
   |         |          |  |  |       |  |
   X-------->|          |  |  |       |  |  Request
   |         X--------->|->|->|       |  |  Prepare(1)
   |         |<---------X--X--X       |  |  Promise(1,{Va,Vb,Vc})
   |         X--------->|->|->|       |  |  Accept!(1,Vn)
   |         |<---------X--X--X------>|->|  Accepted(1,Vn)
   |<---------------------------------X--X  Response
   |         |          |  |  |       |  |
```

