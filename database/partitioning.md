# Partitioning

## Partitioning of Key-Value Date

### By Key Range

* **Assigning a continuous range of keys to each partition.**
* **Pro:** In each partition, values are sorted by key, so **Range Query** is easy.
* **Con:** Load may not evenly spread across the partitions, can lead to **hot spots**. \(For example: **timestamp** is the key\)

### By Hash of Key

* **Using Hash function to determine the partition for a given key.**
* **Pro:** Distributed keys **fairly** among the partitions.
* **Con:** Can't do **Range Query**.

{% hint style="warning" %}
**Cassandra** are using **compound primary key**, first part of the key determine the partition, and second part used for sorting in SSTable.
{% endhint %}

### How to deal with hot spots?

????

## Secondary Indexes

### By Document

* Each partition maintains its own **Local Index**, covering only the data in that partition.
* To search for something , you need to send **query to all partitions**, and combine all results get back.
* Used By: **MongoDB**, **Cassandra**, **Riak**.

### By Term

* **Global index** and **Distributed** to different partitions.
* Pro: **Read more efficient**. no need to broadcasting.
* Con: **Writes are slower**, a single write will affect multiple partitions for updating the secondary indexes.

## Rebalancing

The process of moving load from one node in the cluster to another.

### Why Rebalance partitions?

* Query throughput increase, need more **CPU**
* Data size increases, need more **storage**.
* Machine fails, need other machine to **take over**.

{% hint style="danger" %}
**Why not just mod by number of Node?**

If the number of nodes N changes most of the keys need to be moved from one node to another. We shouldn't move data around more than necessary.
{% endhint %}

### Fixed number of partitions

* Each node has many partitions, the number of total partitions are fixed.
* When partitions grow big, move some to a new node.
* **Con**: If boundaries set wrong, some partition could be very big, and some are empty.

### Dynamic partitioning

* Partition size should be in a range.
* **Split** if partition too large, **merge** if too small. Number of partitions not fixed.
* Used by **MangoDB**, **HBase.**
* Suitable for **range-partitioned** and also **hash-partitioned** data.

### Partitioning proportionally to nodes \(Consistent Hashing\)

* Used by **Cassandra** , which has a fixed number partitions per nodes.
* When add new node, randomly chose a fixed number of partitions to split and move date to the new node.
* The randomization can produce unfair split, but considering every node has a large number of partitions \(at least 256 in Cassandra\), the new node ends up taking a fair share of load.

{% hint style="info" %}
**Consistent Hashing:**

1. Given a list of nodes, hash them to integers in the range and map to hash ring.
2. To map a key to a node,
   * Hash it to a single integer.
   * Move clockwise on the ring until finding the first node it encounters.
   * That node is the one that contains the key.
3. We add “virtual node” for nodes. Instead of mapping each node to a single point on the ring, we map it to multiple points on the ring.
{% endhint %}

## Request Routing

How does client know which node has which data?

We need a separate **coordination service** to keep track of this cluster metadata.

* **Kafka**, **HBase** : ZooKeeper.
* **MongoDB**: its own config server.
* **Cassandra**, **Riak**: Gossip protocol.
  * Requests can be sent to any node, and will will be forwards to the right node that has the requested partition.
  * Complex but avoid a external coordination service.

## Examples

### MongoDB

![mongodb-sharded-cluster-production-architecture](https://docs.mongodb.com/manual/_images/sharded-cluster-production-architecture.bakedsvg.svg)

#### 结构

* **shard**: Each shard contains a subset of the sharded data. Shards must be deployed as a replica set.
  * Primary Shard : Each database in a sharded cluster has a primary shard that holds all the un-sharded collections for that database. Each database has its own primary shard. The primary shard has no relation to the primary in a replica set.
* **mongos**: The mongos acts as a query router, providing an interface between client applications and the sharded cluster. The mongos tracks what data is on which shard by caching the metadata from the config servers. 
  * The most common practice is to run mongos instances on the same systems as your application servers, but you can maintain mongos instances on the shards or on other dedicated resources.
* **config servers**: Config servers store metadata and configuration settings for the cluster. Config servers must be deployed as a replica set \(CSRS\).

#### shard key

* The shard key is either an indexed field or indexed **compound** fields that exists in every document in the collection.

#### hash sharding

* Hashed Sharding involves computing a hash of the shard key field’s value. Each chunk is then assigned a range based on the hashed shard key values.
* 不支持range query, 但是更离散, provides more even data distribution across the sharded cluster

#### range sharding

* Ranged sharding involves dividing data into ranges based on the shard key values. Each chunk is then assigned a range based on the shard key values.
* A range of shard keys whose values are “close” are more likely to reside on the same chunk. This allows for targeted operations as a mongos can route the operations to only the shards that contain the required data.
* 支持range query

