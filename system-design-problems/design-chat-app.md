---
description: 'Wechat, Facebook messenger, Line, ....'
---

# Design Chat App

## Scenario

### Feature

* **P2P chat**
* **Group chat**
* Contect
* Sign in/up \(user system\)

### QPS

* WeChat has 45B messages per day.
* QPS: 45B / 86400 ~ 520K
* Peak: 1.5M
* Storage 30Byte \* 45B = 1.3T per day

## Service

* Message Service
* Realtime Service \(Push Notification\)

## Storage

### Message Service

**Schema**

![](../.gitbook/assets/chat_sys.png)

* Massage table
  * **NoSQL**:
    * Optimize for write
    * Msgs are append only, no need for modifications.
    * No complex index.
  * Row\_key : Thread\_id
  * Column\_key: Created\_at
* Thread table:
  * **SQL or NoSQL**: 
    * No Complex index
* User Thread  table:
  * SQL or **NoSQL**: 
    * No Complex index
  * Row\_key: User\_id
  * Column\_key: Updated\_at \(Since we need to update the data happend after last fetch\)

#### **How to get all msg in a Thread?**

* **SELECT** from Massage table
* **WHERE** thread\_id = xxxxx
* **ORDER** by Creat\_at
* **LIMIT** 20

#### **What id user has personal setting for each thread?**

* Add User\_thread table

#### **How to get Thread\_id if front end only give participant user id?**

* Add field **Participants\_hash\_code** to Thread table
* Participants\_hash\_code = Hash\(sorted\(participants\_user\_ids\)\)
* We can use UUID as Hash Function to prevent hash collision.
* If using **NoSQL**, we need add another table to search from **Participants\_hash\_code** to Thread\_id.

### Realtime Service

* Android GCM and IOS APNS
  * Can not support web client
* Socket
  * Build connection
    * Client request web server for push server IP.
    * Client connect with push server.
  * Send MSG
    * User A send MSG to web server.
    * Web server store the MSG, and push to push server.
    * push server send MSG to User B.
    * User B receive the MSG.
* Backup, what if push server down
  * Client poll for update every 10s.

## Scale

What about Group Chat \(500 people\)?

* Only few people online.
* Web server send request to push server 500 times would be a waste.

### Channel Service

* 增加一个Channel Service（频道服务） 
* 为每个聊天的Thread增加一个Channel信息 
* 对于较大群，在线用户先需要订阅到对应的 Channel 上
  * 用户上线时，Web Server \(message service\) 找到用户所属的频道（群），并通知 Channel Service 完成订阅 
  * Channel就知道哪些频道里有哪些用户还活着 
  * 用户如果断线了，Push Service 会知道用户掉线了，通知 Channel Service 从所属的频道里移除
* Message Service 收到用户发的信息之后
  * 找到对应的channel 
  * 把发消息的请求发送给 Channel Service 
  * 原来发500条消息变成发1条消息
* Channel Service 找到当前在线的用户
  * 然后发给 Push Service 把消息 Push 出去





### Typy Indicator

“对方正在打字”这个功能是怎么实现的？

* 首先 Storage 这个方面其实这个信息是不需要存储的，即便要存储（比如为了做数据分析）也就是存储最后一次 type 的时间就好了。 
* client A 检测到用户正在输入以后，发一个轻量级的消息给 serve.
* server 通过 push service 发正在输入的信息给 client B，然后 client B 就显示用户正在输入
* 约定一个检测时间，比如当用户在输入框里输入的时候，大概每隔1-3s 就发送一次这种信息 push（具体取几秒钟看产品经理怎么拍脑袋了）。

### Multi-device log in

怎样实现不允许两个手机同时登录微信功能?

* 如从手机登陆时，查询是否已经有其他手机处于登陆状态, By device\_id.
* 如果没有，则创建新的 session.
* 如果有，将对应的 session 设为 expire 或者删除，并发送 push notification 让已经登录的手机 logout
  * 如果 Push Notification 失败也没有关系 
  * 该手机会在下次访问任何API的时候发现自己已经logout了并跳转至登入界面

### Online Status

* Add User\_status table \(id, User\_id, Last\_update\_at\)
* Client update it's User\_table every 3s.
* Client check friend's status at the same time.

