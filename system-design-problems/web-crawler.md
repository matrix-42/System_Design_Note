# Web Crawler

## Senario

### Feature

* Craw pages
* Store pages for indexer to builder invert index

### QPS

* 10 days to crawed 1B pages \(1k web pages per sec\)
* Storage: 10 T \(10 kB per web page\)

## Service

* Crawler service
  * BFS

## Storage

* Message Queue 
  * as _Queue_
* Distributed file system
* Key-value Database
  * as _hashset_

## Scale

### 如何限制爬虫访问某个网站的频率？

* 单纯的使用先进先出的 Queue 会使得一个网站短时间内被抓取次 数过多，从而导致爬虫被封等问题
* 让 Crawler 只做 Consumer，不负责产生新的抓取任务 新增一个 Scheduler \(Producer\) 负责调度和生产抓取任务 在 Database 中记录每个网站下一次可以友好抓取的时间
  * Crawler --&gt; DB --&gt; Scheduler
  * DB 中增加 key=domain value=url\_list 的存储结构，存放每个域名下面待抓取的 URL List 
  * DB 中增加每个 domain 下次什么时候可以被抓取的信息记录 
  * Scheduler 的代码，循环遍历每个 domain，遇到可以抓取的 domain，就把其中的一个 url 丢到抓取队列 中

### 在中国抓取美国的网页会比较慢?

* 在不同的地区部署 Crawler 系统 
* 每个地区只抓取所在地区被分配的 domain 下的网页 
* 可以通过 domain 的 whois 信息来确定网站所属地区

### 如何处理网页的更新和失效？

* 很多时候网页会不断更新，爬虫也需要不断的抓取同一个网页 有的时候可能网站挂了一小段时间，此时网页正好无法被抓取
* 增加对每个 URL 的信息记录, 记录下这个 URL 下一次需要被重新抓取的时间, 可以通过 Expenential Backoff 的方式计算:
* 如何处理网页更新
  * URL 抓取成功以后，默认 1 小时以后重新抓取
  * 如果 1 小时以后抓取到的网页没有变化，则设为 2 小时以后再次抓取, 2小时以后还是没有变化，则设为 4 小时以后重新抓取，以此类推 
  * 如果 1 小时以后抓取到的网页发生变化了，则设为 30 分钟以后再次抓取, 30分钟以后又变化了，设为 15 分钟以后重新抓取。
  * 设置抓取时间的上下边界，如至少 30 天要抓取一次，至多 5 分钟抓取一次
* 如何处理网页失效
  * URL 抓取失效以后，默认 1 小时以后重新抓取
  * 如果 1 小时以后依然抓不到，则设置为 2 小时
  * 其他步骤类似网页更新的情况





