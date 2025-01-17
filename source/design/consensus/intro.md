# 共识算法介绍

共识算法是指在分布式场景中，多个节点为了达成相同的数据状态而运行的一种分布式算法。 在分布式场景中，可能出现网络丢包、时钟漂移、节点宕机、节点作恶等等故障情况，共识算法需要能够容忍这些错误，保证多个节点取得相同的数据状态。

根据使用场景的不同，又可将共识算法分为公链共识、联盟链共识两类。本链为联盟链，采用的是联盟链共识算法中的PBFT共识算法。



## PBFT共识算法

#### 角色划分

- **Client**：客户端节点，负责发送交易请求。
- **Primary**: 主节点，负责将交易打包成区块和区块共识，每轮共识过程中有且仅有一个Primary节点。
- **Replica**: 副本节点，负责区块共识，每轮共识过程中有多个Replica节点，每个Replica节点的处理过程类似。

其中，Primary和Replica节点都属于共识节点。

- **view**: 视图，一个view中存在一个主节点和多个副本节点，它描述了一个多副本系统的当前状态。另外，节点是在同一个view上对数据达成共识，不能跨域view。



#### 算法流程

PBFT 算法的基本流程主要有以下四步：

1. 客户端发送请求给主节点

2. 主节点广播请求给其它节点，节点执行PBFT算法的**三阶段共识流程**。

3. 节点处理完三阶段流程后，返回消息给客户端。

4. 客户端收到来自 f+1 个节点的相同消息后，代表共识已经正确完成

   

#### 三阶段共识流程

![](../../../img/1735889898585.png)



##### PRE-PREPARE

主节点给收到的消息分配一个编号n，接着广播一条< PRE-PREPARE, v, n, d>, m>消息给其他副本节点，并将请求记录到本地历史（log）中。

说明：n主要用于对所有客户端的请求进行排序；v指视图编号；m指消息内容；d指消息摘要。

从< PRE-PREPARE, v, n, d>可以看出，请求消息本身内容（m）是不包含在预准备的消息里面的，这样就能使预准备消息足够小。预准备消息的目的是作为一种证明，确定该请求是在视图v中被赋予了序号n，从而在视图变更的过程中可以追索。 副本节点收到主节点的< PRE-PREPARE, v, n, d>, m>消息，需要进行以下校验：

REQUEST和PRE-PREPARE消息中签名是否正确。

当前视图编号是v。

该节点从未在视图v中接受过序号为n但是摘要d不同的消息m。

m的消息摘要与消息中d是否一致。

判断n是否在区间[h, H]内,如果验证不通过，则丢弃，否则接受消息，于是进入PREPARE阶段



##### PREPARE

当前副本节点广播一条< PREPARE, v, n, d, i>消息，并且将预准备消息和准备消息写入自己的消息日志。i是当前节点编号。

主节点和副本节点收到< PREPARE, v, n, d, i>消息，需要进行以下校验 ：

消息签名是否正确。

判断n是否在区间[h, H]内。

d是否和当前已收到PRE-PPREPARE中的d相同

如果验证不通过，则丢弃，否则接受消息。PREPARE准备阶段完成的条件为：当前节点i将(m,v,n,i)写入消息日志，从2f个不同副本节点收到的与预准备消息一致的准备消息。满足这两个条件后进入COMMIT阶段。



##### COMMIT

当前节点广播一条< COMMIT, v, n, d, i>消息。

主节点和副本节点收到< COMMIT, v, n, d, i>消息，需要进行以下校验：

COMMIT消息签名是否正确。

当前副本节点是否已经收到了同一视图v下的n。

计算m的摘要，并判断和d是否一致。

n是否在区间[h, H]内。

如果验证不通过，则丢弃，否则接受消息。

COMMIT阶段完成的条件是：

任意f+1个正常副本节点集合中prepared(m,v,n,i)为真，这一条确保committed(m,v,n)为真。 prepared(m,v,n,i)为真，并且节点i已经接受了2f+1个确认（包括自身在内）与预准备一致的消息。这一条确保committed-local(m,v,n,i)为真。确认与预准备消息一致的条件是具有相同的视图编号、消息序号和消息摘要。
