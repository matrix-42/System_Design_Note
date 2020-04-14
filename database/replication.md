# Replication

## Purpose of Replication

* **加速** To keep data close to user to reduce latency
* **容灾** To keep system continually working even if some parts failed
* **分流** To serve more read queries and increase read throughput

## Three main Algorithms

* Single leader \(**MySQL**, **MongoDB**\)
* Multi leader \(multi data-center\)
* leaderless \(**Dynamo**, **Cassandra, Riak**\)

## Read and write 

* Client write to leader
* Leader sand data to followers \(WAL\)
* Client can read from either leader or followers

> WAL:  All modifications are written to a log before they are applied

## Failure handling

### Follower failure

* Follower keep a log of changes received form the leader.
* When follower crashes and restart, it connect to leader and request all newer changed based on it's own log.
* Follower catch up by apply these changes.

### leader failure

* Determining that leader has failed. \(**heartbeat**\)
* Choosing a new leader.
  * Through election process or appointed by controller node
  * Usually the new leader should have the most up-to-date date form the old leader.
* Re-configuring the system to use the new leader. \(**Zookeeper?**\)

### Add new follower

* Leader continuely take snapshot.
* Follower copy hte snapshot.
* Follower request all changes after snapshot has taken.

## Problem of Replication

Replication can be synchronous and asynchronous.

* Synchronous: the leader wait until followers confirmed received the write before report to user
* Asynchronous: the leader send the massage, don't wait for a response from the followers.

**Asynchronous can be fast but when Replication lag increases server could fail.**

**Solution: Transaction** 

\*\*\*\*

## **Examples:**

* MySQL : master and 2 slave, [write-ahead logging](https://en.wikipedia.org/wiki/Write-ahead_logging)
* MongoDB : Primary and 2 secondary, [write-ahead logging](https://en.wikipedia.org/wiki/Write-ahead_logging)
* Cassandra : Multi virtual node in consistent hashing ring.

### Cassandra vs MongoDB

* MongoDB和Cassandra各自的数据可用性策略可能是它们之间最大的不同。 在Cassandra部署中，你可以设置多个主节点。如果一个或多个主节点发生故障，只要至少有一个主节点仍然存在，那你的数据库仍然将保持可用状态。这种高度分布式和冗余的模型使得在Cassandra中很容易实现高可用性（HA），当然，前提是你可以使用备用基础架构来设置多个主节点。 \(将数据“顺时针”存储在 Consistent hashing 环上的三个 virtual nodes 中\)
* 另一方面，MongoDB也没有忽略高可用性的问题，但其策略是基于自动故障转移的想法。你只能在MongoDB集群中设置一个主节点。如果主站发生故障，从站节点将自动转变为新的主站点。这确保了连续性，但这不会立即发生，通常需要将近一分钟。 \(p-s-s模型\)
* 你是否可以接受一分钟的数据存储中断，可能取决于你正在尝试做的事情。但是，无论你怎么对比，Cassandra都比MongoDB更具弹性和高可用性。

  [原文链接](https://searchdatabase.techtarget.com.cn/7-23287/)

