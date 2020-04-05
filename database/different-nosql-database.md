---
description: 四种不同的非关系型数据库
---

# Different NoSQL Database

## Four types of NoSQL Database

### Key-Value Store 

* _**Redis**_
* _**MemcacheDB**_
* _**Riak**_

The value is a blob that the data store just stores, without caring or knowing what’s inside; it’s the responsibility of the application to understand what was stored. 

**Pros:** Great performance, Easy to scale 

**Cons:** No Columns

### Documented Store 

* _**MongoDB**_
* _**Apache CouchDB**_

A document database is, at its core, a key/value store with one major exception. **Instead of just storing any blob in it, a document db requires that the data will be store in a format that the database can understand.** The format can be XML, JSON, Binary JSON \(MongoDB\), or just about anything, as long as the database can understand it. Which means that we can now allow **queries on the document data. \(for example, a secondary index\)**

Document stores allow you to play with your data and store it however you see fit. They have fast writing, good query times based on indexing, but the main advantage is the **schema flexibility** and nestibility.



### Column Store

* _**Cassandra**_ 
* _**Hbase**_

The main advantages of storing data in columns over relational DBMS are fast search/access and data aggregation. Relational databases store a single row as a continuous disk entry. Different rows are stored in different places on the disk while columnar databases store all the cells corresponding to a column as a continuous disk entry, thus making the **search/access faster.**

Each column family can be compared to a container of rows in an RDBMS table, where the key identifies the row and the row consists of multiple columns. The difference is that various rows do not have to have the same columns, and columns can be added to any row at any time without having to add them to other rows.

This allows for much faster querying and processing of data while storing data that's somewhat related

Many consider wide-column stores to be two-dimensional key-value databases

### Graph Store





{% embed url="https://opensourceforu.com/2017/05/different-types-nosql-databases/" %}

{% embed url="https://www.quora.com/What-are-the-main-differences-between-the-four-types-of-NoSql-databases-KeyValue-Store-Column-Oriented-Store-Document-Oriented-Graph-Database" %}

\*\*\*\*

