# Partitioning

## MongoDB

![mongodb-sharded-cluster-production-architecture](https://docs.mongodb.com/manual/_images/sharded-cluster-production-architecture.bakedsvg.svg)

### 结构

* **shard**: Each shard contains a subset of the sharded data. Shards must be deployed as a replica set.
  * Primary Shard : Each database in a sharded cluster has a primary shard that holds all the un-sharded collections for that database. Each database has its own primary shard. The primary shard has no relation to the primary in a replica set.
* **mongos**: The mongos acts as a query router, providing an interface between client applications and the sharded cluster. The mongos tracks what data is on which shard by caching the metadata from the config servers. 
  * The most common practice is to run mongos instances on the same systems as your application servers, but you can maintain mongos instances on the shards or on other dedicated resources.
* **config servers**: Config servers store metadata and configuration settings for the cluster. Config servers must be deployed as a replica set \(CSRS\).

### shard key

* The shard key is either an indexed field or indexed **compound** fields that exists in every document in the collection.

### hash sharding

* Hashed Sharding involves computing a hash of the shard key field’s value. Each chunk is then assigned a range based on the hashed shard key values.
* 不支持range query, 但是更离散, provides more even data distribution across the sharded cluster

### range sharding

* Ranged sharding involves dividing data into ranges based on the shard key values. Each chunk is then assigned a range based on the shard key values.
* A range of shard keys whose values are “close” are more likely to reside on the same chunk. This allows for targeted operations as a mongos can route the operations to only the shards that contain the required data.
* 支持range query

