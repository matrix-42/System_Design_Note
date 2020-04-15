# NewsFeed

{% hint style="danger" %}
API Design
{% endhint %}

## Scenario

### Feature

* Time line
* News feed
* post 

### QPS

* DAU
  * 100M
* Read QPS 
  * 100M \* 100 / 86400 ~ 100k
  * Peak Read QPS 300k
* Write QPS 3k

## Service

### Tweet service

* Timeline
* Newsfeed
* Post

### API design

## Storage

### Pull model

**schema**

                       ![](../.gitbook/assets/user_table.png)![](../.gitbook/assets/tweet_table.png)            

                                                             ![](../.gitbook/assets/picture1%20%281%29.png)

**Post tweet:**

* Add 1 tow to tweet table

**News Feed:**

* Query Friendship Table to find user's followings.
* Query Tweet Table find every following's 100 most tweet
* K-merge sort, send back to user.

### Push model

**+  Newsfeed Table**

                                                              ![](../.gitbook/assets/newsfeed_table.png) 

**Post tweet:**

* Query Friendship Table, find followers
* Add rows to NewsFeed Table, owner is the followers.

**News feed:**

* Query NewsFeed Table, find their feeded tweet.



### Pull VS. Push, Pros and Cons

**Pull:** 

* Pro: 
  * Post tweet  =&gt; 1次DB write
* Con: 
  * News Feed =&gt; N次DB Reads非常慢 且发生在用户获得News Feed的请求过程中

**Push:** 

* Pro: 
  * News Feed =&gt; 1次DB Read
* Con: 
  * Post tweet  =&gt; N个粉丝，需要N次DB Writes, followers的数目可能很大
  * 好处是可以用异步任务在后台执行 \(Async Tasks Server\)，无需用户等待

## Scale

### Pull model 

#### Read latency

* **Cache** every user's **Timeline**
  * latest 1000 tweets.
* **Cache** every user's **News Feed**
  * Just access DB for tweets that created after last cached News feed.
  * If cache missed, just do K-merge sort like before.

#### Sharding

* Sharding Tweet Table by owner\_id, since we need to query owner\_id to pull tweets

### Push model

#### Waste storage space 

* don't need, disk is cheap

#### Fanout is painful for celebrities \(may need hours!\)

* Push 结合 Pull 的优化方案
* 普通的用户仍然 Push 
* 将 Lady Gaga 这类的用户，标记为明星用户 \(在usertable 里增加一栏\)
* 对于明星用户，不 Push 到用户的 News Feed 中 
* 当用户需要的时候，来明星用户的 Timeline 里取，并合并到 News Feed 里

#### Sharding

* Sharding NewsFeed Table by owner\_id, since we need to query owner\_id to pull tweets



{% hint style="info" %}
* 什么时候用 Push？
  * 资源少 
  * 想偷懒，少写代码 
  * 实时性要求不高 
  * 用户发帖比较少 
  * 双向好友关系，没有明星问题（比如朋友圈）
* 什么时候用 Pull ?
  * 资源充足 \(memory\)
  * 实时性要求高 
  * 用户发帖很多 
  * 单向好友关系，有明星问题
{% endhint %}



