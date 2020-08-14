# Redis



## Nosql的常见分类

​    

  

* 键值对数据库   Redis  Memcache
* 列存储数据库   Hbase Cassandra
* 文档型数据库   MongoDB CouchDB
* 图形数据库       Neo4j  FlockDB

​    

   

​    

## 缓存方案对比 

## 

###  Ehcache



*优点*

* 基于java开发
* 基于JVM缓存



*缺点*

* 不支持集群
* 不支持分布式



### Memcache



*优点*

* key-value形式存储
* 内存使用率高
* 多核处理，多线程

*缺点*

* 无法容灾
* 无法持久化





### Redis



*优点*

* 丰富的数据结构 
* 持久化
* 支持主从同步，故障转移
* 内存数据库



*缺点*

* 单线程





### String类型



##### string字符串

> string:最简单的字符串类型键值对存储

##### key相关

> keys * :查看所有key
>
> type key : key的类型

##### string类型

> get/set/del:查询，设置，删除
>
> 
>
> set key value ex time :设置带过期时间的数据
>
> 
>
> expire key :设置过期时间
>
> 
>
> ttl: 查看剩余时间，-1永不过期 -2 过期
>
> 
>
> append key:合并字符串
>
> 
>
> strlen key :字符串长度
>
> 
>
> incr key :累加1
>
> 
>
> decr key :累减1
>
> 
>
> incrby key num:累加给定数值
>
> 
>
> decrby key num:累减给定数值
>
> 
>
> getrange key start end 截取数据，end = -1代表最后
>
> 
>
> setrange key start newdata 从start位置开始替换数据
>
> 
>
> mset 连续设值
>
> 
>
> mget 连续取值
>
> 
>
> mset 连续设值，如果存在则不设值

##### 其他

> select index 切换数据库 总计默认16个
>
> 
>
> flushdb 删除当前下边db中所有的数据
>
> 
>
> flushall 删除所有db中的数据

##### hash

> hash:类似map，存储结构化数据结构，比如存储一个对象

> hset key field value
>
> > -hset user name 1234
>
> hget user name
>
> 
>
> hmset :设值对象中多个键值对
>
> 
>
> hmsetnx:设值对象多个键值对，存在则不添加
>
> 
>
> hmget 获得对象中多个属性
>
> 
>
> hgetall user 获取对象中整个内容
>
> 
>
> hincrby user age 2 累加属性
>
> 
>
> hincrbyfloat user age 2.2 累加属性
>
> 
>
> hlen user 有多少个属性
>
> 
>
> hexists user age 判断属性是否存在
>
> 
>
> hkeys user 获得所有属性
>
> 
>
> hvals user 获得所有值
>
> 
>
> hdel user 删除对象
>
> 



##### list

> lpush userList 1 2 3 4 5 构建一个list，从左边开始存入
>
> 
>
> rpush userList 1 2 3 4 5 构建一个list，从右边开始存入
>
> 
>
> llen list list长度
>
> 
>
> lindex list index 获取list下标的值
>
> 
>
> lset list index value 把某个下标的值替换
>
> 
>
> linsert list before/after value 插入一个新的值
>
> 
>
> lrem list num value 删除几个相同数据
>
> 
>
> ltrim list start end 截取值，替换原来的list



##### zset

> sorted set 排序set，可以去重可以排序，比如可以根据用户积分做排名，积分作为set的一个数值，根据数值可以做排序，set中的每一个memeber都带一个分数

> zadd zset 10 value 1 20 value2 30 value3 设置member和对应的分数
>
> 
>
> zrange zset 0 -1查看所有zset中的内容
>
> 
>
> zrange zset 0 -1 withscores 带有分数
>
> 
>
> zrank zset value 获取对应下标
>
> 
>
> zscore zset value 获得对应的分数
>
> 
>
> zcard zset 统计个数
>
> 
>
> zcount zset 分数1 分数2 统计个数
>
> 
>
> zrangebyscore zset 分数1 分数2 查询分数之间的member(包含分数1 分数2)
>
> 
>
> zrangebyscore zset (分数 （分数2 查询分数之间的member(不包含分数1 和分数2))
>
> 
>
> zrangebyscore zset 分数 分数2 limit start end 查询分数之间的member(包含分数1 分数2，获得的结果集再次根据下表区间做查询)
>
> 
>
> zrem zset value 删除member



### Redis的线程模型

> 线程模型执行流程
>
> ​	1.redis-server启动，连接应答处理器创建read(AE_Readable)事件
>
> ​    2.redis-cli 通过socket客户端与redis-server建立socket连接
>
> ​    3.server-socker将请求转交给多路复用器
>
> ​	4.多路复用器交给文件事件分配器(队列)
>
> ​	5.连接应答处理器与客户端进行匹配后将请求转交给命令请求处理器
>
> ​	6.命令请求处理器识别并执行命令
>
> ​	7.命令回复处理器write(AE_Writable)标记回写内容
>
> ​	8.redis-cli进行回写write交给server-socket
>
> ​	9.多路复用器将请求丢给文件事件分配器
>
> ​	10.匹配命令回复处理器write事件，执行回写ok等操作

![redis线程模型111](https://github.com/xufeifan1992/note/blob/master/images/20200814/redis/1597371614037.jpg)

> redis-cli 客户端socket
>
> redis-server
>
> * 多路复用器
> * 文件时间分发器
>   * 连接应答处理器   用来与客户端保持连接
>   * 命令请求处理器
>   * 命令回复处理器
