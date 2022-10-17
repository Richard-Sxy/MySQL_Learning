**问题一：**分布式服务器session对象不一致，共享问题。

1.session存储到客户端cookie（不安全）

2.session复制到各个分布式服务器（数据冗余）

3.存储到NoSQL数据库（完全在内存中，速度快）

**问题二**：NoSQL数据库可以解决IO压力

将查询频繁的数据缓存到NoSQL数据库

### NoSQL

**适用场景**

对数据高并发的读写。

海量数据的读写。

对数据高可扩展性的。

### Redis

几乎覆盖了Memcached的绝大部分功能数据都在内存中，支持持久化，主要用作备份恢复除了支持简单的key-value模式，还支持多种数据结构的存储，比如list、set、 hash、 zset等。一般是作为缓存数据库辅助持久化的数据库。

<img src="images/images_20221015180325.png">

````shell
redis.server 前台启动
redis-server /etc/redis.conf 后台启动

redis-cli 连接服务端

redis-cli shutdown 关闭
````

redis默认提供16个数据库，默认用0数据库

select 8 切换到8数据库

#### redis特点

1. 单线程+多路IO复用

2. 支持多种数据类型

3. 支持持久化

#### 常用数据结构

**value类型**

**String字符串**

String是Redis最基本的类型，你可以理解成与Memcached 一模一样的类型，一个key
对应一个value。

String类型是二进制安全的。 意味着Redis的string 可以包含任何数据。比如jpg图片
或者序列化的对象。

底层数据结构为**动态字符串**

String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M

````shell
set key value 设置key value
get key 取值
append key value 追加value
strlen(key) 获取值长度

setnx k1 v1 值存在设置不成功

mset k1 v1 k2 v2 设置多个key value
mget k1 k2 k3 获取多个值
msetnx k11 v11 k12 v12 k1 v11 任意一个存在全设置不成功

getrange name 0 3 范围查询
setrange key 3 abc 在第三位置设置abc

setex key 过期时间 value 

getset key value 取到旧值，被新值替换

incrby key 存的数值加1 原子操作（不会被线程调度机制打断）
decrby key 存的数值减1 原子操作

keys * 查看key

exists key 查看key存在

type key 查看key类型

del key 删除key（直接删除）

unlink key 删除key（选择非阻塞删除，异步删除）

expire key 10 设置key的过期时间

ttl key 查看key多少秒过期 -1表示永不过期，-2表示已经过期

select 1 切换数据库

dbsize 查看当前数据库key的数量

flushdb 清空当前库

flushall 清空所有数据库
````

**List链表**

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部(左边)或者尾部(右边)。它的底层实际是个**双向链表**，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

Redis将链表和ziplist结合起来组成了quicklist。 也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

```shell
lpush k1 value1 value2 设置值 value被头插法放入
lrange k1 0 -1 取所有值
rpush k1 v1 v2 v3 设置值尾插法
lpop k1
rpop k2 取完值，键也没有
rpoplpush k1 k2 右边取放到左边
lindex k2 0 获取索引位置
llen k2 获取长度
linsert k2 before[after] v11 v22 在链表v11前面或后面加入值v22
lrem k2 2 v2 从左边删除n个value
lset k2 0 v3 将第0个位置替换为v3
```

**Set集合**

Redis的Set是string类型的无序无重复元素集合。它底层其实是一个value为null的hash表，所以添加，删除，查找的复杂度都是O(1)。

```shell
sadd k1 v1 v2 v3
smembers k1
sismember k1 v1 判断k1集合是否有v1
scard k1 返回集合元素个数
srem k1 v1 v2 删除元素
spop k1 随机取出值
srandmember k1 n 随机取n个值，不会从集合中删除
smove k1 k2 v3 将k1的v3移动到k2
sinter k2 k3 返回交集数据
sunion k2 k3 返回并集
sdiff k2 k3 返回差集
```

**Hash哈希**

Redis hash是一个键值对集合。 
Redis hash是一个string 类型的field 和value的映射表，hash特别适合用于存储对象。
类似Java里面的Map<String，Object>。

Hash类型对应的数据结构是两种: ziplist (压缩列表)，hashtable (哈希表)。当field-value长度较短且个数较少时，使用ziplist, 否则使用hashtable。

```shell
hset k1 id 1
hget k1 name
hmset k1 id 1 name zhangsan
hexists k1 id 查看id是否存在
hkeys k1
hvals k1
hincrby k1 id 3
hsetnx k1 id 1 存在不能加
```

**Zset 有序集合**

Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每个成员都关联了一个评分( score) ，这个评分( score )被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的 ，但是评分可以是重复了。
因为元素是有序的，所以你也可以很快的根据评分( score )或者次序( position )来获取一个范围的元素。
访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表。

底层结构：hash结构（key为值，value为scores） 或者 跳表

```shell
zadd k1 200 java 300 c++ 400 mysql 500 redis
zrange topn 0 -1
zrange topn 0 -1 withscores
zrangebyscore topn 300 500 withscores 从小到大
zrevrangebyscore topn 500 200 withscores 从大到小
zincrby topn 50 java 增加scores
zrem topn java 删除指定
zcount topn 200 300 统计
zrank topn java 查询排名
```

#### 配置文件

```shell
#支持远程访问
# bind 127.0.0.1 -::1
protected-mode no 

tcp-backlog 511 #连接队列大小
timeout 0 #永不超时
tcp-keepalive 300 #检查心跳时间

databases 16

#设置密码
```

#### 发布和订阅

```shell
subscribe channel1 #订阅频道
publish channel1 hello #发布消息
```

#### 新数据类型

**Bitmaps（实际就是字符串）**

```shell
setbit k1 1 1
setbit k1 10 1
getbit k1 1 #偏移量
bitcount k1
bitcount k1 1 2
bitop and newname k1 k2
```

**HyperLogLog**

基数问题

```shell
pfadd k1 value
pfcount k1
pfmerge k1 k2
```

**Geospatial**

地理位置，经纬度的操作

```shell
geoadd k1 106.50 29.53 chongqing
geopos k1 shanghai
geodist k1 shanghai chongqing km 两个位置的直线距离
georadius k1 110 30 1000 km 一个范围内的地点
```

#### Jedis

```xml
//依赖
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>3.6.3</version>
</dependency>

<!-- redis-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
	<version>2.4.5</version>
</dependency>
<!-- spring2.X 集成redis 所需common-pool2 -->
<dependency>
	<groupId>org.apache.commons</groupId>
	<artifactId>commons-pool2</artifactId>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>
```

```java
#Redis服务器地址
spring.redis.host=192 .168.140.136
#Redis服务器连接端口;
spring.redis.port=6379
#Redis数据库索引(默认为0)
srping.redis.database=0
#连接超时时间(毫秒)
spring.redis.timeout=1800000
#连接池最大连接数(使用负值表示没有限制)
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接↓
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

#### 事务

**Multi** 开启事务 组队

**Exec** 提交事务 提交队列

**Discard** 取消组队

组队阶段出错，整个队列取消

提交队列出错，出错的那个成员取消

**事务冲突**

- 悲观锁

  只要操作就加锁，操作完才释放锁

- 乐观锁

  不会上锁，通过给数据加版本号进行操作

**watch**

在执行multi之前，先执行watch key1 [key2]，可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key被其他命令所改动，那么事务将被打断。（乐观锁使用）

unwatch取消监视

**Redis事务三特性**

- 单独的隔离操作 
  事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 没有隔离级别的概念 
  队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行。
- 不保证原子性 
  事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

连接池复用连接，解决连接超时问题。

**秒杀超卖问题解决：**

1.watch

2.开启事务

3.提交事务

但是这种情况会出现**库存遗留问题**，商品没卖掉

库存遗留问题解决方法：

**Lua脚本语言**

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。
LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作。
但是注意redis的lua脚本功能，只有在Redis 2.6 以上的版本才可以使用。
利用lua脚本淘汰用户，解决超卖问题。
redis 2.6版本以后，通过lua脚本解决争抢问题，实际上是redis利用其单线程的特性，用任务队列的方式解决多任务并发问题。

#### 持久化操作

**RDB（Redis DataBase）**

在指定时间间隔内将内存中的数据集快照写入磁盘。

Redis会单独创建( fork)一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的**缺点**是最后一次持久化后的数据可能丢失。

**写时复制技术**：需要两倍空间

**AOF（Append Only File）**

以日志的形式来记录每个写操作(增量保存)，将 Redis执行过的所有写指令记录下来(读操作不记录)，只许追加文件但不可以改写文件，redis 启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

RDB和AOF同时开启，系统默认取AOF数据

**异常恢复**：可以利用自带命令对损坏的aof文件，进行恢复

AOF可以设置同步频率

#### 主从复制

主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主, Slave以读为主。

- 读写分离，性能扩展
- 容灾快速恢复，一台从服务器宕机，可以切换到其他服务器

info replication 命令打印信息

slaveof ip port 配置从服务器

**一主二从**

主挂了，从还是主服务器的从服务器

主从复制步骤：

1、 当从连接上主服务器之后，从服务器向主服务发送进行数据同步消息
2、主服务器接到从服务器发送过来同步消息，把主服务器数据进行持久化到rdb文件，把rdb文件发送从服务器，从服务器拿到rdb进行读取
3、每次主服务器进行写操作之后，和从服务器进行数据同步

**薪火相传**

**反客为主**

slaveof no one 把从机变成主机

**哨兵模式（sentinel）**

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

sentinel.conf

sentinel montior masterName ip port

启动：redis-sentinel sentinel.conf

**主节点选举规则：**

根据slave-priority=12 值越少优先级越高

偏移量是指获得原主机数据最全的

每个redis实例启动后都会随机生成一个40位的runid

- 选择优先级靠前的
- 选择偏移量最大的
- 选择runid最小的

旧主机变为新主机的从节点

