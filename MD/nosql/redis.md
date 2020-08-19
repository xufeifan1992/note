

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

![redis线程模型](https://github.com/xufeifan1992/note/blob/master/images/20200814/redis/1597371614037.jpg)

> redis-cli 客户端socket
>
> redis-server
>
> * 多路复用器
> * 文件时间分发器
>   * 连接应答处理器   用来与客户端保持连接
>   * 命令请求处理器
>   * 命令回复处理器
>   * 111

### Redis持久化



#### RDB

> 每隔一段时间，吧内存中的数据写入磁盘的临时文件，作为快照，  恢复的时候把快照文件读进内存，如果宕机重启，那么会丢失内存里的数据



​     

> 备份与恢复
>
> ​	内存备份:磁盘临时文件
>
> ​    临时文件:恢复到内存





> * 优势
>   * 1.每隔一段时间备份，全量备份
>   * 2.灾备简单，可以远程传输
>   * 3.子进程备份的时候，主进程不会有任何IO操作,保证数据备份的完整性
>   * 4.相对AOP来说，当有更大文件的时候可以快速恢复系统
> * 劣势
>   * 1.发生故障时，有可能会丢失最后一次的备份数据
>   * 2.紫禁城所占用的内存会和父进程一模一样，会造成CPU负担
>   * 3.由于定时全量备份是重量级操作，所以对于实时备份就无法处理





> > RDB的配置
>
> * 保存位置，可以在redis.conf自定义
> * 保存机制
>   * save 900 1 如果一个缓存更新，则15分钟后备份
>   * save 300 10 如果10个缓存更新，则5分钟后备份
>   * save 60 10000   如果10000个缓存更新，则1分钟后备份



> 1.stop-writes-on-bgsave-error
>
> * yes 如果save过程出错，则停止写操作
> * no  可能造成数据不一致
>
> 2.rdbcompression
>
> * yes 开启rdb压缩模式
> * no  关闭，会节约cpu顺灏，但是文件会大
>
> 3.rdbchecksum
>
> * yes 使用CRC64算法校验rdb进行数据校验，有10%性能顺灏
> * no  不校验





> RDB会丢失最后一次备份的rdb文件，但是其实也无所谓，其实也可以忽略不计，毕竟是缓存，丢了就丢了，但是如果追求数据的完整性，那就的考虑使用AOF了。
>
> **AOP特点**
>
> 1. 以日志的形式来记录用户请求的写操作。读操作不会记录，因为写操作才会存存储。
> 2. 文件以追加的形式而不是修改的形式。
> 3. redis的aof恢复其实就是把追加的文件从开始到结尾读取执行写操作。
>
> **AOF优势**
>
> 1. AOF更加耐用，可以以秒级别为单位备份，如果发生问题，也只会丢失最后一秒的数据，大大增加了可靠性和数据完整性。所以AOF可以每秒备份一次，使用fsync操作。
> 2. 以log日志形式追加，如果磁盘满了，会执行 redis-check-aof 工具
> 3. 当数据太大的时候，redis可以在后台自动重写aof。当redis继续把日志追加到老的文件中去时，重写也是非常安全的，不会影响客户端的读写操作。
> 4. AOF 日志包含的所有写操作，会更加便于redis的解析恢复。
>
> **劣势**
>
> 1. 相同的数据，同一份数据，AOF比RDB大
> 2. 针对不同的同步机制，AOF会比RDB慢，因为AOF每秒都会备份做写操作，这样相对与RDB来说就略低。 每秒备份fsync没毛病，但是如果客户端的每次写入就做一次备份fsync的话，那么redis的性能就会下降。
> 3. AOF发生过bug，就是数据恢复的时候数据不完整，这样显得AOF会比较脆弱，容易出现bug，因为AOF没有RDB那么简单，但是呢为了防止bug的产生，AOF就不会根据旧的指令去重构，而是根据当时缓存中存在的数据指令去做重构，这样就更加健壮和可靠了。
>
> **AOF的配置**
>
> ```
> # AOF 默认关闭，yes可以开启
> appendonly no
> 
> # AOF 的文件名
> appendfilename "appendonly.aof"
> 
> # no：不同步
> # everysec：每秒备份，推荐使用
> # always：每次操作都会备份，安全并且数据完整，但是慢性能差
> appendfsync everysec
> 
> # 重写的时候是否要同步，no可以保证数据安全
> no-appendfsync-on-rewrite no
> 
> # 重写机制：避免文件越来越大，自动优化压缩指令，会fork一个新的进程去完成重写动作，新进程里的内存数据会被重写，此时旧的aof文件不会被读取使用，类似rdb
> # 当前AOF文件的大小是上次AOF大小的100% 并且文件体积达到64m，满足两者则触发重写
> auto-aof-rewrite-percentage 100
> auto-aof-rewrite-min-size 64mb
> ```
>
> **到底采用RDB还是AOF**
>
> 1. 如果你能接受一段时间的缓存丢失，那么可以使用RDB
> 2. 如果你对实时性的数据比较care，那么就用AOF
> 3. 使用RDB和AOF结合一起做持久化，RDB做冷备，可以在不同时期对不同版本做恢复，AOF做热备，保证数据仅仅只有1秒的损失。当AOF破损不可用了，那么再用RDB恢复，这样就做到了两者的相互结合，也就是说Redis恢复会先加载AOF，如果AOF有问题会再加载RDB，这样就达到冷热备份的目的了。

​    

### 主从原理



> Redis Master : 主
>
> Redis Slave: 从
>
> * 初始化时，slave把RDB文件数据复制过来，slave将RDB下载到自己的硬盘后加载到内存里，之后每一次写操作都会将命令同步给slave，进行数据同步
> * 如果slave宕机重启，将会从master中进行增量恢复
> * 如果master宕机并且没有开启持久化，master重启后会清空slave中的数据
> * 常用一master对应2slave，太多了同步时会影响性能
>
> 



### 无磁盘化复制

> 内存之间socket传输
>
> repl-diskless-sync no 



### 缓存过期机制

> ### 已过期的key如何处理？
>
> 设置了expire的key缓存过期了，但是服务器的内存还是会被占用，这是因为redis所基于的两种删除策略
>  redis有两种策略：
>
> 1. （主动）定时删除
>    - 定时随机的检查过期的key，如果过期则清理删除。（每秒检查次数在redis.conf中的hz配置）
> 2. （被动）惰性删除
>    - 当客户端请求一个已经过期的key的时候，那么redis会检查这个key是否过期，如果过期了，则删除，然后返回一个nil。这种策略对cpu比较友好，不会有太多的损耗，但是内存占用会比较高。
>
> 所以，虽然key过期了，但是只要没有被redis清理，那么其实内存还是会被占用着的。
>
> 
>
> ### 那么如果内存被Redis缓存占用慢了咋办？
>
> 
>
>  内存占满了，可以使用硬盘，来保存，但是没意义，因为硬盘没有内存快，会影响redis性能。
>  所以，当内存占用满了以后，redis提供了一套缓存淘汰机制：MEMORY MANAGEMENT
>
> `maxmemory`：当内存已使用率到达，则开始清理缓存
>
> ```java
> * noeviction：旧缓存永不过期，新缓存设置不了，返回错误
> * allkeys-lru：清除最少用的旧缓存，然后保存新的缓存（推荐使用）
> * allkeys-random：在所有的缓存中随机删除（不推荐）
> * volatile-lru：在那些设置了expire过期时间的缓存中，清除最少用的旧缓存，然后保存新的缓存
> * volatile-random：在那些设置了expire过期时间的缓存中，随机删除缓存
> * volatile-ttl：在那些设置了expire过期时间的缓存中，删除即将过期的
> ```



### redis哨兵模式

Master挂了，如何保证可用性，实现继续读写

### 什么是哨兵

Sentinel(哨兵)是用于监控Redis集群中Master状态的工具，是 Redis 高可用解决方案，哨兵可以监视一个或者多个redis  master服务，以及这些master服务的所有从服务；当某个master服务宕机后，会把这个master下的某个从服务升级为master来替代已宕机的master继续工作。

### ![图片描述](https://climg.mukewang.com/5df0815b08d0c20113620706.jpg)



### 配置哨兵监控master

创建并且配置sentinel.conf：

- 普通配置

  ```xml
  port 26379
  pidfile "/usr/local/redis/sentinel/redis-sentinel.pid"
  dir "/usr/local/redis/sentinel"
  daemonize yes
  protected-mode no
  logfile "/usr/local/redis/sentinel/redis-sentinel.log"
  ```

- 核心配置

  ```xml
  # 配置哨兵
  sentinel monitor mymaster 127.0.0.1 6379 2
  # 密码
  sentinel auth-pass <master-name> <password>
  # master被sentinel认定为失效的间隔时间
  sentinel down-after-milliseconds mymaster 30000
  # 剩余的slaves重新和新的master做同步的并行个数
  sentinel parallel-syncs mymaster 1
  # 主备切换的超时时间，哨兵要去做故障转移，这个时候哨兵也是一个进程，如果他没有去执行，超过这个时间后，会由其他的哨兵来处理
  sentinel failover-timeout mymaster 180000
  ```

### 启动哨兵 x 3

redis-sentinel sentinel.conf

### 测试

1. master挂了，看slave是否成为master
2. master恢复，观察slave状态

### 结论

master挂了以后，由于哨兵监控，剩余slave会进行选举，选举后其中一个成为master，当原来的master恢复后，他会成为slave。