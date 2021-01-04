[原文](https://www.kubernetes.org.cn/6226.html)

etcd 诞生于 CoreOS 公司，它最初是用于解决集群管理系统中 OS 升级的*分布式并发控制以及配置文件的存储与分发*等问题。基于此，etcd 被设计为提供*高可用、强一致的小型 keyvalue 数据存储服务*。

一个 etcd 集群，通常会由 3 个或者 5 个节点组成，多个节点之间通过 *Raft一致性算法*完成分布式一致性协同，算法会选举出一个主节点作为 leader，由 leader 负责数据的同步与数据的分发。当 leader 出现故障后系统会自动地选取另一个节点成为 leader，并重新完成数据的同步。客户端在多个节点中，仅需要选择其中的任意一个就可以完成数据的读写，内部的状态及数据协同由 etcd 自身完成。

在 etcd 整个架构中，有一个非常关键的概念叫做 quorum(法定人数)，quorum 的定义是 （n+1）/2，也就是说超过集群中半数节点组成的一个团体，在 3 个节点的集群中，etcd 可以容许 1 个节点故障，也就是只要有任何 2 个节点可用，etcd 就可以继续提供服务。同理，在 5 个节点的集群中，只要有任何 3 个节点可用，etcd 就可以继续提供服务，这也是 etcd 集群高可用的关键。

在允许部分节点故障之后继续提供服务，就需要解决一个非常复杂的问题：分布式一致性。在 etcd 中，该分布式一致性算法由 Raft 一致性算法完成。
Raft 一致性算法能够工作的一个关键点是：任意两个 quorum 的成员之间一定会有一个交集（公共成员），也就是说只要有任意一个 quorum 存活，其中一定存在某一个节点（公共成员），它包含着集群中所有的被确认提交的数据。正是基于这一原理，Raft 一致性算法设计了一套数据同步机制，在 Leader 任期切换后能够重新同步上一个 quorum 被提交的所有数据，从而保证整个集群状态向前推进的过程中保持数据的一致。

我们可以通过 etcd 提供的客户端去访问集群的数据，也可以直接通过 http 的方式（类似 curl 命令）直接访问 etcd。在 etcd 内部，其数据表示也是比较简单的，我们可以直接把 etcd 的数据存储理解为一个有序的 map，它存储着 key-value 数据。同时 etcd 为了方便客户端去订阅数据的变更，也支持了一个 watch 机制，通过 watch 实时地拿到 etcd 中数据的增量更新，从而实现与 etcd 中的数据同步等业务逻辑。

Put(key, value) / Delete(key)
第一组是 Put 与 Delete。上图可以看到 put 与 delete 的操作都非常简单，只需要提供一个 key 和一个 value，就可以向集群中写入数据了，删除数据的时候只需要指定 key 即可；
Get(key) / Get(keyFrom, keyEnd)
第二组是查询操作。etcd 支持两种类型的查询：第一种是指定单个 key 的查询，第二种是指定的一个 key 的范围；
Watch(key / keyPrefix)
第三组是数据订阅。etcd 提供了 Watch 机制，我们可以利用 watch 实时订阅到 etcd 中增量的数据更新，watch 支持指定单个 key，也可以指定一个 key 的前缀，在实际应用场景中的通常会采用第二种形势；
Transactions(if / then / else ops.).Commit()
第四组事务操作。etcd 提供了一个简单的事务支持，用户可以通过指定一组条件满足时执行某些动作，当条件不成立的时候执行另一组操作，类似于代码中的 if else 语句，etcd 确保整个操作的原子性；
Leases: Grant / Revoke / KeepAlive
第五组是 Leases 接口。Leases 接口是分布式系统中常用的一种设计模式，其用法后面会具体展开。

## 数据版本机制
term 代表的是整个集群 Leader 的任期。当集群发生 Leader 切换，term 的值就会 +1。在节点故障，或者 Leader 节点网络出现问题，再或者是将整个集群停止后再次拉起，都会发生 Leader 的切换。
revision 代表的是全局数据的版本。当数据发生变更，包括创建、修改、删除，其 revision 对应的都会 +1。特别的，在集群中跨 Leader 任期之间，revision 都会保持全局单调递增。正是 revision 的这一特性，使得集群中任意一次的修改都对应着一个唯一的 revision，因此我们可以通过 revision 来支持数据的 MVCC，也可以支持数据的 Watch。

put key1: term = 2, rev = 6
put key2: term = 2, rev = 7
delete key1: term=2, rev = 8
put key2: term = 2, rev = 9
  restart etcd...
put key3: term = 3, rev = 10
put key4: term = 3, rev = 11
delete key2: term = 3, rev = 12

对于每一个 KeyValue 数据节点，etcd 中都记录了三个版本：
+ 第一个版本叫做 create_revision，是 KeyValue 在创建时对应的 revision；
+ 第二个叫做 mod_revision，是其数据被操作的时候对应的 revision；
+ 第三个 version 就是一个计数器，代表了 KeyValue 被修改了多少次。



## mvcc(multi-version concurrency control) & streaming watch
在 etcd 中支持对同一个 Key 发起多次数据修改，每次数据修改都对应一个版本号。etcd 在实现上记录了每一次修改对应的数据，也就就意味着一个 key 在 etcd 中存在多个历史版本。在查询数据的时候如果不指定版本号，etcd 会返回 Key 对应的最新版本，当然 etcd 也支持指定一个版本号来查询历史数据。
```
  put(key, value1) rev = 5
  put(key, value2) rev = 6
  get(key) -> value2
  get(key, rev = 5) -> value1
```
```
watcher = Watch(key, rev)
for {
  event = watcher.Recv()
  handle(event)
  ...
}
```

因为 etcd 将每一次修改都记录了下来，使用 watch 订阅数据时，可以支持从任意历史时刻（指定 revision）开始创建一个 watcher，在客户端与 etcd 之间建立一个数据管道，etcd 会推送从指定 revision 开始的所有数据变更。etcd 提供的 watch 机制保证，该 Key 的数据后续的被修改之后，通过这个数据管道即时的推送给客户端。

![](https://ucc.alicdn.com/pic/developer-ecology/c42d42cec9964c4b9f05f361799769f9.png)
