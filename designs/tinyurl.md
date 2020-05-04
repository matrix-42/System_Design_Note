# TinyURL

{% hint style="danger" %}
Need to consider more features
{% endhint %}

## Scenario

### **F**eature

* long URL -&gt; short URL
* short URL -&gt; long URL -&gt; page redirection
* 1 to 1 or 1 to many?
* delete old record?

### QPS

* How many User active per day?
  * 100M
* Assume every user short URL every 10 days
  * Average Write QPS = 100M \* 0.1/86400 ~100
  * **Peak Write QPS =** 100 \* 2 = **200**
* Assume every user click shorten URL every 1 day
  * Average Read QPS = 100M \* 1/86400 ~1k
  * **Peak Read QPS =** 1k \* 2 = **2k**
* Assume each URL need 100B to store

  * 100M \* 0.1 = 10M URL per day
  * **Storage needed per day =** 100M \* 100B = **1G**

  \*\*\*\*

## **Service**

Just one service:  Tiny URL service

### API

* UrlService.encode\(long\_url\)
  * POST /data/shorten/
  * Data = &lt;long\_url&gt;
  * return short URL
* UrlService.decode\(short\_url\)
  * GET /&lt;short\_url&gt;
  * return Http redirect response

{% hint style="danger" %}
api 具体的结构

需要做 project 才知道
{% endhint %}

### Algorithms

1. Random Generate + Check if valid
   * 随机一个 6 位的 ShortURL，如果没有被用过，就绑定到该 LongURL
   * 优点：实现简单 
   * 缺点：生成短网址的速度随着短网址越来越多变得越来越慢
2. **Offline Key Generate Service \(Editor's pick\)**
   * 建立一个独立的 service: 生成随机的6 的ShortURL, TinyURL service 每次可以 request一个新的.
   * 优点：No need to Check if valid
   * 缺点：独立的 service, replica
3. Base62 + Sequential ID 
   * Use Base 62 to represent Primary Key -&gt; Sequential ID
   * 优点：效率高 
   * 缺点：依赖于全局的自增ID

## Database

* SQL
  * If use Base62 approach,  need Sequential ID support
  * Primary key =  Squential\_ID, column = LongURL
* **NoSQL \(Editor's pick\)**
  * Easy to scale
  * high performance
  * Since NoSQL usually don't support secondary index, we need 2 table to be able to search by long\_url and short\_url
    * row\_key=longURL, column\_key=ShortURL, value=null or timestamp
    * row\_key=shortURL, column\_key=LongURL, value=null or timestamp

## Scale

How to reduce response time?

### **Cache Aside**

* long to short
* short to long

### **Scale server**

* 不同的地区，使用不同的 Web 服务器
* 通过DNS解析不同地区的用户到不同的服务器

### **Scale Database**

* Memcached 跨地区分布
* shard database
  * shard by long key
  * shard by short key



\*\*\*\*

\*\*\*\*

