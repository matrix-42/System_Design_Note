---
description: 四种不同的非关系型数据库
---

# Different NoSQL Database

## Key-Value Store 

* _**Redis**_
* _**MemcacheDB**_
* _**Riak**_

The value is a blob that the data store just stores, without caring or knowing what’s inside; it’s the responsibility of the application to understand what was stored. 

**Pros:** Great performance, Easy to scale 

**Cons:** No Columns

## Documented Store 

* _**MongoDB \(B-Tree\)**_
* _**Apache CouchDB**_

A document database is, at its core, a key/value store with one major exception. **Instead of just storing any blob in it, a document db requires that the data will be store in a format that the database can understand.** The format can be XML, JSON, Binary JSON \(MongoDB\), or just about anything, as long as the database can understand it. Which means that we can now allow **queries on the document data. \(for example, a secondary index\)**

Document stores allow you to play with your data and store it however you see fit. They have fast writing, good query times based on indexing, but the main advantage is the **schema flexibility** and nestibility.

## Wide Column Store \(Bigtable Like\)

* _**Cassandra**_ 
* _**Hbase**_

Many consider wide-column stores to be two-dimensional key-value databases

Databases that are similar to key-value but allow a very **large number of columns**. They are well suited for analyzing huge data sets.

Each column family can be compared to a container of rows in an RDBMS table, where the key identifies the row and the row consists of multiple columns. The difference is that various rows do not have to have the same columns, and columns can be added to any row at any time without having to add them to other rows.

{% hint style="warning" %}
Wide column stores 并不是 column oriented storage, 因为column family 内, 数据是 row oriented 的. 
{% endhint %}

Wide column stores 是基于 **bigtable** model 的, 所以在需要大量的写时, 或者连续读时, 效率很高. \(Like Wechat\)

## Graph Store

....



{% embed url="https://opensourceforu.com/2017/05/different-types-nosql-databases/" %}

{% embed url="https://www.quora.com/What-are-the-main-differences-between-the-four-types-of-NoSql-databases-KeyValue-Store-Column-Oriented-Store-Document-Oriented-Graph-Database" %}

\*\*\*\*

