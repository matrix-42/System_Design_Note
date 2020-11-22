# Some Database

* [MySQL](some-database.md#mysql)
* [MongoDB](some-database.md#mongodb)
* [Cassandra](some-database.md#cassandra)

## MySQL

### 1. 结构

* table : 一级结构
* row : table 中的每一行称作 row
* field : row 中每一列称为field

### 2. primary key

* 对于关系表，有个很重要的约束，就是任意两条记录不能重复。不能重复不是指两条记录不完全相同，而是指能够通过某个字段唯一区分出不同的记录，这个字段被称为主键。
* 主键应该是唯一的，没有意义的。
* 联合主键：关系数据库实际上还允许通过多个字段唯一标识记录，即两个或更多的字段都设置为主键，这种主键被称为联合主键。对于联合主键，允许一列有重复，只要不是所有主键列都重复即可
* 表的主键、外键必须有索引

### 3. foreign key

* 另一个table的 primary key
* 表的主键、外键必须有索引

### 4. auto increment id

* 会在新记录插入表中时生成一个唯一的数字。
* 默认地，AUTO\_INCREMENT 的开始值是 1，每条新记录递增 1

### 5. transaction?

* 把多条语句作为一个整体进行操作的功能, 被称为 transaction \(数据库事务\).
* 数据库事务可以确保该事务范围内的所有操作都可以全部成功或者全部失败. 如果事务失败, 那么效果就和没有执行这些SQL一样, 不会对数据库数据有任何改动.

### 6. index

* 索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。通过使用索引，可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，这样就大大加快了查询速度。
* 索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，那么索引效率越高。反过来，如果记录的列存在大量相同的值，例如gender列，大约一半的记录值是M，另一半是F，因此，对该列创建索引就没有意义。
* 可以对一张表创建多个索引。索引的优点是提高了查询效率，缺点是在插入、更新和删除记录时，需要同时修改索引，因此，索引越多，插入、更新和删除记录的速度就越慢。
* 对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一

### 7. 什么是复合索引 \(composite index\)

* 索引可以包含一个、两个或更多个列。两个或更多个列上的索引被称作复合索引。
* 复合索引的结构与电话簿类似，人名由姓和名构成，电话簿首先按姓氏对进行排序，然后按名字对有相同姓氏的人进行排序。如果您知道姓，电话簿将非常有用；如果您知道姓和名，电话簿则更为有用，但如果您只知道名不姓，电话簿将没有用处。
* 类似于字典顺序!!

## MongoDB

[官方文档](https://docs.mongodb.com/manual/sharding/)

### 1. 结构

* collection : 一级结构
* document : collection 中的每一行称作 document

### 2. index

* MongoDB 的index 是local 的 类似Cassandra 的 Secondary index.
* For an update, remove, find - if mongos is not given a shard key - then it’s going to have to broadcast the request to all the different shards that cover the collection.
* 相当于MongoDB clustering key 和 secondary key 合并为index.

### 2. 没有SQL中的 relation

* MongoDB 将数据存储为一个文档，数据结构由键值\(key=&gt;value\)对组成. MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组

### 4. Faster than SQL?

{% embed url="https://stackoverflow.com/questions/9702643/mysql-vs-mongodb-1000-reads" %}

## Cassandra

[官方文档](http://cassandra.apache.org/doc/latest/architecture/overview.html)

### 1. 结构

* **column family** : 一级结构
* **row** : column family 中的每一行称作 row

### 2. primary key

#### **partition key:**

* sharding定位, primary key 的 1st column
* generates a token hash value that determines which node in a cluster the partition will be stored on

#### **clustering key:**

* 用来在partition里排序, 可以是复合的. primary key 的剩下的 column
* 可以这样理解: partition key 规定了partition的排列方式, clustering key 规定了数据在partition里的排列方式, 相当于每个partition里的 primary key.
* 为什么需要column key: 数据在磁盘里肯定是顺序存储的, 不论有没有给定排列方式. 不如给一个常用的排列方式. And Cassandra is designed to perform sequential reads for query results.

### 3. secondary index

* A secondary index is pretty similar to what we know from regular relational databases. If you have a query with a where clause that uses column values that are not part of the primary key, lookup would be slow because a full row search has to be performed. Secondary indexes make it possible to service such queries efficiently. Secondary indexes are stored as extra tables, and just store extra data to make it easy to find your way in the main table.
* **Secondary indexes are implemented as local indexes -- they are not distributed in the cluster**
* 如果需要filter 的 column 不是clustering key, 则我们可以加一个secondary index, 来加速查找.
* [用法](https://stackoverflow.com/questions/29692738/how-do-secondary-indexes-work-in-cassandra):
  * `select * from TABLE_NAME where primary_key=some_value and secondary_index=some_value`
  * Query with only secondary index is also possible in Cassandra `select * from TABLE_NAME where secondary_index=some_value` But this has a large impact on fetching data, because it reads all partitions on distributed environment. The data fetched by this query is also not consistent and could not relay on it.

### 其他资料:

* [clustering key vs secondary index](https://stackoverflow.com/questions/24622511/what-is-the-difference-between-a-clustering-column-and-secondary-index-in-cassan)
* [concept of indexing in apache cassandra](https://www.geeksforgeeks.org/concept-of-indexing-in-apache-cassandra/)
* [cassandra data modeling](https://dzone.com/articles/cassandra-data-modeling-primary-clustering-partiti)

