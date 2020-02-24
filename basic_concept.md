# 结构
## 1. SQL -- mySQL 结构
### 1. table
* 一级结构
* table 中的每一行称作 row
### 2. primary key
* 主键应该是唯一的，没有意义的。
* 表的主键、外键必须有索引
### 3. foreign key
* 另一个table的 primary key
* 表的主键、外键必须有索引

### 4. auto increment id
* 会在新记录插入表中时生成一个唯一的数字。
* 默认地，AUTO_INCREMENT 的开始值是 1，每条新记录递增 1

### 5. transaction?
* 把多条语句作为一个整体进行操作的功能, 被称为 transaction (数据库事务).
* 数据库事务可以确保该事务范围内的所有操作都可以全部成功或者全部失败. 如果事务失败, 那么效果就和没有执行这些SQL一样, 不会对数据库数据有任何改动.

### 6. 什么是复合索引 (composite index)
* 索引可以包含一个、两个或更多个列。两个或更多个列上的索引被称作复合索引。
* 复合索引的结构与电话簿类似，人名由姓和名构成，电话簿首先按姓氏对进行排序，然后按名字对有相同姓氏的人进行排序。如果您知道姓，电话簿将非常有用；如果您知道姓和名，电话簿则更为有用，但如果您只知道名不姓，电话簿将没有用处。
* 类似于字典顺序!!

## 2. NOSQL-- MongoDB 结构
### 1. collection 
* 类似于 table
### 2. document
* 类似于 row
### 3. 没有SQL中的 relation
* MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成. MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组
# Replica
* MongoDB和Cassandra各自的数据可用性策略可能是它们之间最大的不同。 在Cassandra部署中，你可以设置多个主节点。如果一个或多个主节点发生故障，只要至少有一个主节点仍然存在，那你的数据库仍然将保持可用状态。这种高度分布式和冗余的模型使得在Cassandra中很容易实现高可用性（HA），当然，前提是你可以使用备用基础架构来设置多个主节点。 (将数据“顺时针”存储在 Consistent hashing 环上的三个 virtual nodes 中)
* 另一方面，MongoDB也没有忽略高可用性的问题，但其策略是基于自动故障转移的想法。你只能在MongoDB集群中设置一个主节点。如果主站发生故障，从站节点将自动转变为新的主站点。这确保了连续性，但这不会立即发生，通常需要将近一分钟。 (p-s-s模型)
* 你是否可以接受一分钟的数据存储中断，可能取决于你正在尝试做的事情。但是，无论你怎么对比，Cassandra都比MongoDB更具弹性和高可用性。
[原文链接]( https://searchdatabase.techtarget.com.cn/7-23287/)


### 4. Faster than SQL?
* https://stackoverflow.com/questions/9702643/mysql-vs-mongodb-1000-reads

# Sharding
