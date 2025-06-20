

# 一、基础篇

## 1、概述

### 1.1 Redis是什么

- Remote Dictionary Server（**远程字典服务**）
- 使用**ANSIC语言**编写且遵守**BSD协议**，是一个高性能的key-value数据库，提供了丰富的数据结构，例如：String、Hash、List、Set、SortedSet等
- <font color="red">**数据是存储在内存中的**</font>
- Redis支持事务、持久化、LUA脚本、发布\订阅、缓存淘汰、流技术等多种功能特性，提供了主从模式、Redis Sentinel和Redis Cluster集群架构方案
- 总结：<font color="red">**基于内存的KV键值对内存数据库**</font>



### 1.2 能干嘛

- 主流功能与应用

  - **分布式缓存，挡在MySQL数据库之前的带刀护卫**
    - 与传统数据库MySQL关系
      - Redis是key-value数据库(NoSQL一种)，MySQL是关系型数据库
      - <font color="red">**Redis数据操作主要在内存，MySQL主要存储在磁盘**</font>
      - Redis在某些场景中明显优于MySQL，比如计数器、排行榜等方面
      - Redis通常用于一些特定场景，需要和MySQL一起使用
      - **两者并不是相互替换和竞争关系，而实共用和配合使用**

  ![与mysql关系](.\图片\基础\与mysql关系.png)

  - **内存存储和持久化（RDB+AOF）**，Redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务

  - **高可用架构配置**：单机、主从、哨兵、集群

  - **缓存穿透、击穿、雪崩**

  - **分布式锁**

  - **队列**：Redis提供list和set操作，使得Redis能作为一个很好的消息队列平台来使用

  - **排行榜+点赞**：通过zset可以实现

    

### 1.3 优势

- **性能极高**：Redis读的速度是11w次/秒，写的速度是8w1次/秒
- **Redis数据类型丰富**：不仅仅支持简单的key-value类型的数据，同时还支持list、set、zset、hash等数据结构的存储
- **Redis支持数据的持久化**：可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用
- **Redis支持数据的备份**，即：master-slave模式的数据备份



### 1.4 总结

![总结](.\图片\基础\总结.png)



### 1.5 文档地址

- 官方文档英文：https://redis.io/
- 官方文档中文：https://www.redis.cn/
- **中文知识文档（重点）**：**https://redis.com.cn/documentation.html**
- 源码地址：https://github.com/redis/redis
- 在线测试：https://try.redis.io
- **命令参考手册（重点）**：**http://doc.redisfans.com/**



## 2、安装配置

### 2.1 安装前配置

- **Linux环境安装Redis必须先具备gcc编译环境：因为redis是C语言开发的**

  - gcc是 [GNU](https://so.csdn.net/so/search?q=GNU&spm=1001.2101.3001.7020) 编译器集合（GNU Compiler Collection）的核心组件之一，**用于编译 C、C++ 和其他语言的源代码**。它是 Linux 系统和开源项目中最常用的编译工具。
  - 查看是否安装gcc：gcc -v

  ![总结](.\图片\基础\gcc查看.png)

  - 安装gcc：yum -y install gcc-c++

  ![总结](.\图片\基础\gcc安装.png)



### 2.2 下载安装

![总结](.\图片\基础\redis安装步骤.png)

- 1、下载地址：https://redis.io/downloads/，上传到opt目录
- 2、进入到opt目录：cd /opt，解压命令：**tar -zxvf redis-7.4.0.tar.gz**
- 3、进入redis目录：**cd redis-7.4.0**
- 4、执行make编译命令：**make && make install**，直到出现这个就证明编译安装成功

![总结](.\图片\基础\编译安装成功标志.png)

- 5、查看默认安装目录：**usr/local/bin**

  - Linux下的usr/local相当于windows系统中的C:\Program Files
  - 安装后查看：
    - redis-benchmark：性能测试工具，服务启动后运行该命令，查看自己本机性能
    - redis-check-aof：恢复有问题的AOF文件
    - redis-check-dump：修复有问题的dump.rdb文件
    - <font color="red">redis-cli：客户端，操作入口</font>
    - redis-sentinel：redis集群使用
    - <font color="red">redis-server：redis服务器启动命令</font>

  ![总结](.\图片\基础\redis安装路径.png)

- 6、将redis解压包下默认的配置文件redis.conf拷贝到自己定义好的一个路径下
  - mkdir myredis
  - cp redis.conf myredis/redis7.conf
- 7、<font color="red">**修改myredis/redis7.conf配置文件做初始化设置**</font>，改完记得重启才能生效
  - <font color="red">**默认daemonize no  改为  daemonize yes**</font>，意思是改为后台服务端启动
  - <font color="red">**默认protected-mode yes  改为  protected-mode no**</font>，如果想要让别人访问连接，这里的保护模式就得禁用掉
  - <font color="red">**默认bind 127.0.0.1 -::1注释掉，这个意思是默认只能访问本机，不注释掉会影响远程IP访问**</font>
  - <font color="red">**添加redis密码：requirepass li998813**</font>

- 8、**服务端启动（在/usr/local/bin目录下运行redis-server，启用myredis/redis7.conf配置文件）**：redis-server myredis/redis7.conf
- 9、查看是否启动成功：ps -ef|grep redis|grep -v grep
- 10、连接服务：redis-cli -a li998813 -p 6379，注意：-p不写默认是访问6379
- 11、关闭
  - 单实例关闭：redis-cli -a li998813 shutdown，不写端口默认关闭6379
  - 多实例关闭，指定端口关闭：redis-cli -p 6379 shutdown
- 12、卸载
  - 停止redis-server服务
  - 删除/usr/local/bin目录下与redis所有相关的文件
    - ls -l /usr/local/bin/redis-*
    - rm -rf /usr/local/bin/redis-*



## 3、十大数据类型

![总结](.\图片\数据类型\十大数据类型.png)

- **String（字符串）**
  - String是redis最基本的类型，一个key对应一个value
  - <font color="red">**String类型是二进制安全的**</font>，意思是redis的String可以包含任何数据，比如jpg图片或者序列化的对象
  - String类型redis最基本的数据类型，<font color="red">**一个redis中字符串value最多可以是512M**</font>
- **List（列表）**
  - redis列表是简单的字符串列表，按照插入顺序排序。<font color="red">**可以添加一个元素到列表的头部（左边）或者尾部（右边）**</font>
  - <font color="red">**它的底层实际是一个双端链表**</font>，最多可以包含2^32 - 1（4294967295，每个列表超过40亿个元素）
- **Hash（哈希表）：键值对**
  - Redis hash是一个String类型的field（字段）和value（值）的映射表，hash特别适用于存储对象
  - Redis中每个hash可以存储2^32 - 1键值对（40多亿）
- **Set（集合）**
  - Redis的Set是String类型的<font color="red">**无序集合**</font>，集合成员是唯一的，这就意味着集合中不能出现重复的数据，集合对象的编码可以是intset或者hashtable
  - <font color="red">**Redis中Set集合是通过哈希表实现的，所以添加、删除、查找的时间复杂度都是0(1)**</font>
  - 集合中最大的成员数是2^32 - 1（4294967295，每个集合可存储40多亿个成员）
- **Zset（有序集合），又叫sorted set**
  - Redis的Zset和set一样是String类型元素的集合，且不允许重复的成员
  - <font color="red">**不同的是每个元素都会关联一个double类型的分数**</font>，redis正是通过分数来为集合中的成员进行<font color="red">**从小到大**</font>的排序
  - <font color="red">**Zset的成员是唯一的，但分数（score）却可以重复**</font>
  - <font color="red">**Zset集合是通过哈希表实现的，所以添加、删除、查找的时间复杂度都是0(1)**</font>
  - 集合中最大的成员数是2^32 - 1
- **GEO（地理空间）**
  - 主要用于存储地理位置信息，并对存储的信息进行操作，包括
    - 添加地理位置的坐标
    - 获取地理位置的坐标
    - 计算两个位置之间的距离
    - 根据用户给定的经纬度坐标来获取指定范围内的地理位置集合
- **HyperLogLog（基数统计）**
  - 是用来做<font color="red">**基数统计**</font>的算法，优点是：在输入元素的数量或者体积非常大的时候，计算基数所需的空间总是固定且很小
  - 在redis里，每个HyperLogLog键只需要花费12KB的内存，就可以计算接近2^64个不同元素的基数，这和计算基数时，元素越多耗费内存越多的集合成鲜明对比
  - 但是，每个HyperLogLog指挥根据输入元素来计算基数，并不会存储输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素
- **bitmap（位图）**
  - **由0和1状态表现的二进制位的bit数组**
  - 一个字节（一个byte）= 8位，每位专门用来存储0或者1，每位就是一个bit
- **bitfield（位域）**
  - 通过bitfield命令可以一次性操作多个<font color="red">**比特位域（指的是连续的多个比特位）**</font>，它会执行一系列操作并返回一个相应数组，这个数组中的元素对应参数列表中的相应操作的执行结果
- **Stream（redis流）**
  - Redis Stream主要用于消息队列，redis本身有一个发布订阅（pub/sub）来实现消息队列的功能，但是缺点是消息无法持久化，如果出现网络断开，redis宕机等，消息就会被丢弃。简单来说，发布订阅（pub/sub）可以分发消息，但无法记录历史消息
  - <font color="red">**Redis Stream提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每个客户端的访问位置，还能保证消息不丢失**</font>



### 3.1 Redis键（key）常用命令

- keys *：查看当前库所有的key
- exists key：判断某个key是否存在，如果多个key用空格隔开，返回存在的个数
- type key：查看key是什么类型
- del key：删除指定的key数据
- unlink key：非阻塞删除，仅仅将key从keyspace元数据中删除，真正的删除会在后续异步中操作
- ttl key：查看还有多少秒过期，-1表示永不过期，-2表示已经过期
- expire key：为给定的key设置过期时间，单位秒
- move key dbindex [0-15]：将当前数据库的key移动到给定的数据库db中
- select dbindex：切换数据库[0-15]，默认为0
- dbsize：查看当前数据库key的数量
- flushdb：清空当前库
- flushall：通杀全部库
- config get key：获取配置文件的配置



### 3.2 大小写和帮助命令

- 命令不区分大小写，而key是区分大小写的
- 帮助命令：help @类型
  - help @String
  - help @hash
  - help @list



### 3.3 String命令

#### 3.3.1 set key value设置值

- set key value [NX|XX] [GET] [EX seconds|PX milliseconds|EXAT unix-time-seconds|PXAT unix-time-milliseconds|KEEPTTL]
  - 可选参数
    - EX seconds：以秒为单位设置过期时间
    - PX milliseconds：以毫秒为单位设置过期时间
    - EXAT timestamp：设置以秒为单位的UNIX单位时间戳所对应的时间为过期时间
    - PXAT milliseconds-timestamp：设置以毫秒为单位的UNIX单位时间戳所对应的时间为过期时间
    - NX：键不存在的时候设置值
    - XX：键存在的时候设置值
    - KEEPTTL：保留设置前指定键的生存时间
    - GET：返回指定键原本的值，若键不存在时返回nil

~~~bash
# NX：键不存在的时候设置值。
# 场景1：没有k1
set k1 v1 nx
# 结果：OK，即：成功
# 场景2：若已经有k1
set k1 v1 nx
# 结果：(nil)，即：失败

# XX：键存在的时候设置值。
# 场景1：没有k1
set k1 v1 xx
# 结果：(nil)，即：失败
# 场景2：若已经有k1
set k1 v1xx xx
# 结果：OK，即：成功，此时k1=v1xx


# get：先返回原来的值，再设置新值，若键不存在时返回nil
set k1 v2 get
# 结果：v1xx

# ex: 设置有效时间，单位：秒
set k1 v1 ex 100
ttl
# 结果：93（还有多少秒过期）

# ex: 设置有效时间，单位：秒
set k1 v1 ex 100
ttl
# 结果：93（还有多少秒过期），过期了会打印-1

# px: 设置有效时间，单位：毫秒
set k1 v1 px 10000
ttl
# 结果：8（还有多少秒过期），过期了会打印-1

# exat timestamp: 设置以秒为单位的UNIX单位时间戳所对应的时间为过期时间，即：到所设置的这个时间戳时间时过期
set k1 v1 exat 1749845320
ttl k1
# 结果: 115174(还有多少秒过期)

# pxat milliseconds-timestamp: 设置以毫秒为单位的UNIX单位时间戳所对应的时间为过期时间，即：到所设置的这个时间戳时间时过期
set k1 v1 pxat 1749845220000
ttl k1
# 结果: 114895(还有多少秒过期)

# keepttl：保留设置前指定键的生存时间
# 场景：设置k1过期时间30s
set k1 v1 ex 30
# 结果：13
set k1 v2 ex 90
# 不用keepttl结果：87，值和过期时间会被覆盖
set k1 v2 keepttl
# 用keepttl结果：10，过期时间保持原来的，只是单纯的值覆盖
~~~



#### 3.3.2 批量设置/获取多个键值

- 批量设置/获取多个键值：
  - 设置：mset key value [key value ...]
  - 获取：mget key [key ...]
  - 批量没有的话设置值：msetnx key value [key value ...]
- 注意：<font color="red">**redis命令是原子性的，所以如果msetnx设置的时候，中间只要有一个key是已经有的，那么所有key值设置都不生效**</font>

~~~bash
# 批量设置
mset k1 v1 k2 v2 k3 v3

# 批量获取
mget k1 k2 k3
# 结果
v1
v2
v3

# 没有的情况，输出nil
mget k1 k5
# 结果
v1
nil

# 批量没有的话设置值
msetnx k4 v4 k5 v5
# 结果：1（生效）

# 中间有一个键是已有的
msetnx k1 v6 k6 v6
# 结果：0（不生效）
~~~



#### 3.3.3 获取指定区间的值

- getrange beginIndex endIndex：获取指定区间的值，类似Java中的substring，endIndex=-1时即到末尾
- setrange beginIndex value：从指定下标开始值覆盖

~~~bash
# 现在有k1 = abcd1234
set k1 abcd1234

# 获取指定区间的值
getrange k1 0 -1
# 结果：abcd1234
getrange k1 0 3
# 结果：abcd
getrange k1 4 -1
# 结果：1234

# 覆盖
setrange k1 0 1234
# 结果：8(8位)，k1=12341234
setrange k1 0 qwertyuiop
# 结果：10(10位)，k1=qwertyuiop
setrange k1 3 aaa
# 结果：10(10位)，k1=qweaaauiop
~~~



#### 3.3.4 数值增减

- <font color="red">**一定要是数字才能进行加减**</font>
- 递增数字：incr key
- 增加指定的整数：incrby key increment
- 递减数字：decr key
- 增减指定的整数：decrby key decrement

~~~bash
# 设置k1=0
set k1 0
# 结果：k1=0

incr k1
# 结果：k1=1

incrby k1 5
# 结果：k1=6

decr k1
# 结果：k1=5

decrby k1 3
# 结果：k1=2
~~~



#### 3.3.5 获取字符串长度和内容追加

- 获取字符串长度：strlen key
- 追加内容：append key value

~~~bash
# 设置k1=abcd1234
set k1 abcd1234

# 获取字符串长度
strlen k1
# 结果：8

# 追加内容
append k1 5678
get k1
# 结果：abcd12345678
~~~



#### 3.3.6 分布式锁

- setnx key value（set if not exist）：没有此键才设置
- setex key ttl value（set with expire）：设置键的时候加上过期时间

~~~bash
# 设置键的时候加上过期时间
setex k1 15 v1
ttl k1
# 结果：13（13秒过期）

# 没有此键设置
setnx k1 v1
# 结果：没有k1时：1；有k1时：0
~~~



#### 3.3.7 先get再set

- getset key value：先get再set 
- 等价于：set key value get

~~~bash
# 先有值：k1=abc
set k1 abc

getset k1 qwe
# 打印：abc，此时k1=qwe
~~~



#### 3.3.8 应用场景

- 通过自增自减实现：抖音点赞某个视频或商品，点一次加一次
- 是否喜欢的文章



### 3.4 List命令

- <font color="red">**一个双端链表的结构**</font>，主要功能有push/pop等，一般用在栈、队列、消息队列等场景
- left、right都可以插入添加
- 如果键不存在，创建新的链表
- 如果键已存在，新增内容
- 如果值全移除，对应的键也就消失了
- <font color="red">**它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会比较差**</font>

![总结](.\图片\数据类型\list.png)



#### 3.4.1 设置/获取值

- lpush key v1 [v2 v3 ...]：从左边开始压入值
- rpush key v1 [v2 v3 ...]：从右边开始压入值
- lrange key begin end：从左边开始获取值

~~~bash
# 从左边开始压入值
lpush k1 1 2 3 4 5

# 从右边开始压入值
rpush k2 11 22 33 44 55

# 获取k1类型
type k1
# 结果：list

# 从左边开始获取所有值
lrange k1 0 -1
# 结果：5 4 3 2 1

# 从左边开始获取指定下标的值
lrange k2 0 2
# 结果：11 22 33
~~~



#### 3.4.2 弹出元素

- lpop key [count]：从左边弹出指定个数元素，没写count就是一个
- rpop key [count]：从右边弹出指定个数元素，没写count就是一个

~~~bash
# 现有数组：5 4 3 2 1
lpop k1
# 结果：5，此时k1=[4 3 2 1]

rpop k1 2
# 结果：1 2，此时k1=[4 3]
~~~



#### 3.4.3 按照索引下标获得元素

- lindex key index：按照索引下标获得元素（从左到右）

~~~bash
# k1=[4,3]
# k2=[11,22,33,44,55]

lindex k1 0
# 结果：4
lindex k2 3
# 结果：44
~~~



#### 3.4.4 获取列表中元素个数

- llen key：获取列表中元素个数

~~~bash
llen k1 
# 结果：2
llen k2
# 结果：5
~~~



#### 3.4.5 删除指定元素

- lrem key N v1：删除N个值等于v1的元素
- 从左往右删除
- lrem key 0 值：表示删除全部给定的值，<font color="red">**零个就是全部值**</font>

~~~bash
# k3=[33 33 33 33 33 22 22 22 22 11 11 11]
lpush k3 11 11 11 22 22 22 22 33 33 33 33 33

# 删除2个值为11元素
lrem k3 2 11
# 结果：k3=[33 33 33 33 33 22 22 22 22 11]

# 删除全部的33
lrem k3 0 33
# 结果：k3=[22 22 22 22 11]


# k4=[11 22 11 22 11 22 11 22]
lpush k4 22 11 22 11 22 11 22 11
# 删除2个11（从左往右删）
lrem k4 2 11
# 结果：k3=[22 22 11 22 11 22]
~~~



#### 3.4.6 保留指定下标区间的值

- ltrim key begin end：保留指定下标区间的值

~~~bash
# list1=[1 2 3 4 5 6 7 8 9]
lpush list1 9 8 7 6 5 4 3 2 1
# 保留4 5 6
ltrim list1 3 5
# 结果：list1=[4 5 6]
~~~



#### 3.4.7 从一个列表出去压入另一个列表

- rpoplpush 源列表 目的列表：移除源列表的最后一个元素，并将该元素添加到另一个列表并返回

~~~bash
# list1=[1 2 3 4 5]
# list2=[6 7 8 9]
lpush list1 5 4 3 2 1
lpush list2 9 8 7 6
rpoplpush list1 list2
# 结果：
# list1=[1 2 3 4]
# list2=[5 6 7 8 9]
~~~



#### 3.4.8 对指定下标设置值

- lset key index value：对指定下标设置值

~~~bash
# list1=[1 2 3 4]
lset list1 0 5
# 结果：list1=[5 2 3 4]
~~~



#### 3.4.9 在已有值前后加值

- linsert key before/after 已有的值 插入的新值
- <font color="red">**只在从左到右第一个前后加**</font>

~~~bash
# list1=[1 2 3 4]
# 在1前面加0
linsert list1 before 1 0
# 结果：list1=[0 1 2 3 4]

# list2=[1 1 2 3]
# 在1后面加5
linsert list2 after 1 5
# 结果：list2=[1 5 1 2 3]
~~~



### 3.5 Hash命令

- KV模式不变，但是这里的V是一个键值对
- 类似Java中的：Map<String, Map<String, String>>



#### 3.5.1 设置/获取值

- hset key field1 value1 field2 value2 ...：设置hash的值
- hget key field1：获取hash的值
- hmset key field1 value1 field2 value2 ...：设置hash的值
- hmget key field1 field2 ...：批量获取值
- hgetall key：获取某个key的所有属性值
- hdel key field1 [field2 ...]：删除key的某些属性
- 注意：<font color="red">**redis后续做了优化，hset现在也可以设置多个值，所以看上去和hmset一样，hmset算是弃用了，后续用hset即可**</font>

~~~bash
# 设置值user01的属性值
hset user01 id 001 name lzy age 18
# 获取user01的属性值
hget user01 name
# 结果lzy

# 批量设置值user02的属性值
hmset user02 id 002 name xhh age 18
# 批量获取user02的值
hmget user02 id name age
# 结果：002 xhh 18

# 获取key的所有值
hgetall user01
# 结果：
1) "id"
2) "001"
3) "name"
4) "lzy"
5) "age"
6) "18"

# 删除user01的age
hdel user01 age
hgetall user01
# 结果：
1) "id"
2) "001"
3) "name"
4) "lzy"
~~~



#### 3.5.2 获取key的全部属性数量

- hlen key：获取key的全部属性数量

~~~bash
# 获取key的全部数量
hlen user01
# 结果：(integer) 2
~~~



#### 3.5.3 判断某个key中是否有field

- hexists key field：判断某个key中是否有field

~~~bash
# 判断某个key中是否有field
hexists user01 id
# 结果：(integer) 1 (有)
hexists user01 xxx
# 结果：(integer) 0 (无)
~~~



#### 3.5.4 获取所有的field/value

- hkeys key：获取key的所有属性
- hvals key：获取key的所有属性值

~~~bash
# 获取key的所有属性
hkeys user01
# 结果：
1) "id"
2) "name"

# 获取key的所有属性值
hvals user01
# 结果：
1) "001"
2) "lzy"
~~~



#### 3.5.5 自增

- hincrby key field increment：将key的field属性自增指定值（整数）
- hincrbyfloat key field increment：将key的field属性自增指定值（浮点数）

~~~bash
# 现有key：
1) "id"
2) "001"
3) "name"
4) "lzy"
5) "age"
6) "18"
7) "score"
8) "79.5"

# 自增整数
hincrby user01 age 2
# 结果：(integer) 20，即：age=18+2=20

# 自增负整数
hincrby user01 age -2
# 结果：(integer) 18，即：age=20-2=18

# 自增小数
hincrbyfloat user01 score 0.5
# 结果："80"，即：age=79.5+0.5=80

# 自增负小数
hincrbyfloat user01 score -0.5
# 结果："79.5"，即：age=80-0.5=79.5
~~~



#### 3.5.6 不存在则赋值，存在则无效

- hsetnx key field value：

~~~bash
# 没有值则赋值成功
hsetnx user01 email 163.com
# 结果：(integer) 1

# 有值则无效
127.0.0.1:6379> hsetnx user01 email 163.com
# 结果：(integer) 0
~~~



### 3.6 Set命令

- Redis的Set是String类型的<font color="red">**无序集合**</font>，集合成员是唯一的，这就意味着集合中不能出现重复的数据，集合对象的编码可以是intset或者hashtable
- <font color="red">**Redis中Set集合是通过哈希表实现的，所以添加、删除、查找的时间复杂度都是0(1)**</font>
- 单值多value，且无重复



#### 3.6.1 添加元素

- sadd key member1 member2 ... ：添加元素
- 值不会重复，如果设置重复的只会塞一次

~~~bash
# 添加不重复的
sadd set1 1 2 3
# 结果：(integer) 3
# set1=[1, 2, 3]

# 添加重复的
sadd set2 1 1 1 2 2 2 3 3 3
# 结果：(integer) 3
# set1=[1, 2, 3]，重复的只会塞一次
~~~



#### 3.6.2 遍历集合中所有元素

- smembers key：遍历集合中所有元素

~~~bash
# 遍历set1
smembers set1
# 结果：
1) "1"
2) "2"
3) "3"

# 遍历set2
smembers set2
# 结果：
1) "1"
2) "2"
3) "3"
~~~



#### 3.6.3 判断元素是否在集合中

- sismember key member：判断元素是否在集合中

~~~bash
# 判断元素有
sismember set1 2
# 结果：(integer) 1

# 判断元素无
sismember set1 5
# 结果：(integer) 0
~~~



#### 3.6.4 删除元素

- srem key member [member ... ]：删除元素

~~~bash
# 删除元素
srem set1 1 2
# 结果：(integer) 2（删除成功2个）
# 此时set1=[3]
~~~



#### 3.6.5 统计集合中元素个数

- scard key：统计集合中元素个数

~~~bash
# 统计set2中有多少个元素
scard set2
# 结果：(integer) 3
~~~



#### 3.6.6 随机展示元素

- srandmember key count：从集合中随机**展示设置的数字个数**元素，且不会删除

~~~bash
# set1=[1,2,3,4,5,6,7,8,9]
srandmember set1 3
# 结果：
1) "6"
2) "5"
3) "8"
# 此时：set1=[1,2,3,4,5,6,7,8,9]
~~~



#### 3.6.7 随机出栈

- spop key count：从集合中随机弹出几个元素，出一个删一个

~~~bash
# set1=[1,2,3,4,5,6,7,8,9]
spop set1 2
# 结果：
1) "2"
2) "5"
# 此时：set1=[1,3,5,6,7,8,9]
~~~



#### 3.6.8 移动元素到另一个集合

- smove key1 key2 value：将key1里的已存在的某个值移动到key2中

~~~bash
# set1=[1,2,3]，set2=[a,b,c]
# 移动已经存在的值
smove set1 set2 2
# 结果：(integer) 1
# 此时：set1=[1,3]，set2=[a,b,c,2]

# 移动已经不存在的值
smove set1 set2 8
# 结果：(integer) 0
~~~



#### 3.6.9 集合运算

- 差集运算A-B，即属于A但不属于B的元素构成的集合：sdiff key [key ...] ，谁在前以谁为基础模板
- 集合的并集运算A∪B，属于A或者属于B的元素合并后的集合：sunion key [key ...] 
- 集合的交集运算A∩B，属于A的同时也属于B的共同元素组成的集合：sinter key [key ...] 
- 获取交集数量：sintercard numkeys key [key ...]  [limit]，即：**它不返回结果，只返回结果的基数，返回由所有给定集合的交集产生的集合的基数**
  - limit参数：显示几个，如果超出最大数量，则返回值就是这个最大数量

~~~bash
# set1=[a b c 1 2], set2=[1 2 3 a x], set3=[1 2 3 m n]
# set1和set2的差集
sdiff set1 set2
# 结果：
1) "b"
2) "c"

# set2和set1的差集
sdiff set2 set1
# 结果：
1) "3"
2) "x"

# set1和set2、set3的差集
sdiff set1 set2 set3
# 结果：
1) "b"
2) "c"

# 求并集
sunion set1 set2 set3
# 结果：
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
6) "m"
7) "3"
8) "x"
9) "n"

# 求交集
sinter set1 set2 set3
# 结果：
1) "1"
2) "2"

# 求交集数量
sintercard 2 set1 set2
# 结果：(integer) 3，即：a 1 2
sintercard 3 set1 set2 set3
# 结果：(integer) 2，即：1 2
sintercard 3 set1 set2 set3 limit 1
# 结果：(integer) 1，加了limit，就返回1
sintercard 3 set1 set2 set3 limit 2
# 结果：(integer) 2，加了limit，就返回2
sintercard 3 set1 set2 set3 limit 3
# 结果：(integer) 2，加了limit，超过最大数了就返回2
~~~



#### 3.6.10 使用场景

- 微信抽奖小程序，spop，出一个就删一个

- 推荐可能认识好友：sdiff

- 微信朋友圈点赞

  - sadd：新增点赞
  - srem：取消点赞
  - smembers：展现所有点赞用户
  - scard：点赞用户数统计
  - sismember：判断某个朋友是否对楼主点赞过

  

### 3.7 Zset命令

- Redis的Zset和set一样是String类型元素的集合，且不允许重复的成员
- <font color="red">**不同的是每个元素都会关联一个double类型的分数**</font>，redis正是通过分数来为集合中的成员进行<font color="red">**从小到大**</font>的排序
- <font color="red">**Zset的成员是唯一的，但分数（score）却可以重复**</font>
- <font color="red">**Zset集合是通过哈希表实现的，所以添加、删除、查找的时间复杂度都是0(1)**</font>
- 总结：在set的基础上，每个val值前加一个score分数值，之前set是k1 v1 v2 v3，现在zset是k1 score1 v1 score2 v2，**向有序集合中加入一个元素和该元素的分数**



#### 3.7.1 添加元素

- zadd key score member [score member ...]：添加元素
- 应用场景：排行榜

~~~bash
zadd zset1 60 v1 70 v2 80 v3 90 v4 100 v5
# 结果：(integer) 5
~~~



#### 3.7.2 遍历元素

- zrange key start stop [withscores]：按照元素分数**从小到大**的顺序返回索引从start到stop之间的所有元素
- zrevrange key start stop [withscores]：按照元素分数**从大到小**的顺序返回索引从start到stop之间的所有元素
- withscores加上则带上分数输出，不加则只输出值

~~~bash
# 从小到大不加分数输出
zrange zset1 0 -1
# 结果：
1) "v1"
2) "v2"
3) "v3"
4) "v4"
5) "v5"

# 从小到大加分数输出
zrange zset1 0 2 withscores
# 结果：
1) "v1"
2) "60"
3) "v2"
4) "70"
5) "v3"
6) "80"


# 从大到小不加分数输出
zrevrange zset1 0 -1
# 结果：
1) "v5"
2) "v4"
3) "v3"
4) "v2"
5) "v1"

# 从大到小加分数输出
zrevrange zset1 0 2 withscores
# 结果：
1) "v5"
2) "100"
3) "v4"
4) "90"
5) "v3"
6) "80"
~~~



#### 3.7.3 获取指定分数范围的元素

- zrangebyscore key [(]min max [withscores] [limit offset count]：获取指定分数范围的元素
- 参数
  - withscores：带分数
  - (：不包含
  - limit：返回限制，limit 开始下标 多少个

~~~bash
# 60到80之间的不带分数
zrangebyscore zset1 60 80
# 结果
1) "v1"
2) "v2"
3) "v3"

# 60到80之间的不带分数
zrangebyscore zset1 60 80 withscores
# 结果
1) "v1"
2) "60"
3) "v2"
4) "70"
5) "v3"
6) "80"

# 60到80之间的不带分数，不包含60
zrangebyscore zset1 (60 80 withscores
# 结果
1) "v2"
2) "70"
3) "v3"
4) "80"

# 60到80之间的不带分数，只要第3个和第4个
zrangebyscore zset1 60 100 withscores limit 2 2
# 结果
1) "v3"
2) "80"
3) "v4"
4) "90"
~~~



#### 3.7.4 获取元素的分数

- zscore key member：获取元素的分数

~~~bash
# 获取元素的分数
zscore zset1 v3
# 结果："80"
~~~



#### 3.7.5 获取集合中元素的数量

- zcard key：获取集合中元素的数量

~~~bash
# 获取集合中元素的数量
zcard zset1
# 结果：(integer) 5
~~~



#### 3.7.6 删除元素

- zrem key member：删除元素

~~~bash
# 删除zset1集合中的v5
zrem zset1 v5
# 结果：(integer) 1
# 此时：zset1为
1) "v1"
2) "v2"
3) "v3"
4) "v4"
~~~



#### 3.7.8 增加某个元素的分数

- zincrby key increment member：增加某个元素的分数

~~~bash
# 增加v4的分数5分
zincrby zset1 5 v4
# 结果："95"
~~~



#### 3.7.9 获取指定分数范围内的元素个数

- zcount key min max：获取指定分数范围内的元素个数

~~~bash
# 获取60到90之间的元素个数
zcount zset1 60 90
# 结果：(integer) 3
~~~



#### 3.7.10 弹出元素

- zmpop numbers key [key ...]  <min | max> [count count]：从键名列表中的第一个非空排序集中弹出一个或多个元素，它们是成员分数对
- 参数
  - numbers：几个集合
  - min | max：最大值或者最小值
  - count：弹出几个
- 注意：弹出元素的是按照集合的顺序，比如zmpop 2 zset1 zset2 min count 2，**则先弹zset1的，直到zset2的全部弹完才动zset2**

~~~bash
# zset1=[60 v1 70 v2 80 v3 95 v4]
# zset2=[1 m1 2 m2 3 m3]
# 从两个集合中弹出2个最小值
zmpop 2 zset1 zset2 min count 2
# 结果：
1) "zset1"
2) 1) 1) "v1"
      2) "60"
   2) 1) "v2"
      2) "70"
~~~







#### 3.7.11 获得下标值

- zrank key value：作用是获取下标值
- zrevrank key value：作用是逆序获取下标值

~~~bash
# zset1=[v1 v2 v3 v4]
# 正序获取v3的下标值
zrank zset1 v3
# 结果：(integer) 2

# 逆序获取v3的下标值
zrevrank zset1 v3
# 结果：(integer) 1
~~~



#### 3.7.12 使用场景

- 根据商品销售对商品进行排序
  - 分数就是销售量
  - member就是商品



### 3.8 bitmap

- 是什么：<font color="red">**由0和1状态表现的二进制位的bit数组**</font>
- 需求场景
  - 用户是否登录过Y、N，比如京东每日签到送京豆
  - 电影、广告是否被点击播放过
  - 钉钉打卡上下班，签到统计
- 说明：<font color="red">**用String类型作为底层数据结构实现的一种统计二值状态的数据类型**</font>
- <font color="red">**位图本质是数组**</font>，它是基于String数据类型的按位的操作。该数组由多个二进制位组成，每个二进制位都对应一个偏移量（我们称之为一个索引）
- Bitmap支持的最大位数是2^32位，它可以极大的节约存储空间，使用512M内存就可以存储多大42.9亿的字节信息（2^32=4294967296）

![总结](.\图片\数据类型\bitmap.png)

- 能干嘛：用于状态统计，Y/N，类似AtomicBoolean



#### 3.8.1 基本命令

|               命令                |                             作用                             | 时间复杂度 |
| :-------------------------------: | :----------------------------------------------------------: | :--------: |
|      setbit key offset value      |                给指定key的值的第offset赋值val                |    O(1)    |
|         getbit key offset         |                   获取指定key的第offset位                    |    O(1)    |
|      bitcount key start end       |            返回指定key中的[start, end]中为1的数量            |    O(n)    |
| bitop operation destkey key [...] | 对不同的二进制存储数据进行位运算（AND、OR、NOT、XOR），destkey 是目标位图 |    O(n)    |
|            strlen key             |        获取key值的大小，这里是获取字节数，8bit=1字节         |            |

~~~bash
# 往k1中第0位和第1位塞1
setbit k1 0 1
setbit k1 1 1

# 获取第0的值
getbit k1 0
# 结果：(integer) 1
# 获取第1的值
getbit k1 1
# 结果：(integer) 1

# 返回指定k1中的[0, 8]中为1的数量
bitcount k1 0 8
# 结果：(integer) 4

# 返回指定k1中1的数量
bitcount k1
# 结果：(integer) 4

# 获取k1的大小长度
strlen k1
# 结果：(integer) 2，16bit=2字节

# 对不同的集合进行位运算
# k1=1010,k2=0101,k3=1111
# 与运算
bitop and k4 k1 k2 k3
# 结果：k4=0000

# 或运算
bitop or k5 k1 k2 k3
# 结果：k5=1111

# 补集：注意补集后只能接单一的位图
bitop not k6 k1
# 结果：k6=0101

# 异或操作
bitop xor k7 k1 k2
# 结果：k7=1111
~~~



#### 3.8.2 使用场景

- 一年时间用户的登陆天数：登录设为1即可，这样设计的好处strlen key，365天才46字节，很节省空间
- 1000w用户也才46M空间
- 此外，实际中使用中，最好对bitmap设置过期时间，让redis自动删除不再需要的签到记录以节省内存性能



### 3.9 HyperLogLog

- 是用来做<font color="red">**基数统计**</font>的算法，优点是：**在输入元素的数量或者体积非常大的时候，计算基数所需的空间总是固定且很小**
- 在redis里，每个HyperLogLog键只需要花费12KB的内存，就可以计算接近2^64个不同元素的基数，这和计算基数时，元素越多耗费内存越多的集合成鲜明对比
- 但是，每个HyperLogLog指挥根据输入元素来计算基数，并不会存储输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素



#### 3.9.1 一些概念

- **基数：是一种数据集，去重复后的真实个数**
  - 例子：U={2,4,6,8,10,4,8,77,39}，去掉重复后的集合U={2,4,6,8,10,77,39}，基数就是7
- 基数统计：用于统计一个集合中不重复的元素个数，就是对集合去重复后剩余元素的计算
- **HyperLogLog就是去重复统计功能的算法**
- 总结：去重脱水后的真实数据



#### 3.9.2 命令

- 添加指定元素到HyperLogLog中：pfadd key element [element ...]
- 返回给定的HyperLogLog的基数估算值：pfcount key [key ...]
- 将多个HyperLogLog合并成一个HyperLogLog：pfmerge destkey sourcekey [sourcekey  ...]

~~~bash
# 添加hll01={1,3,5,7,9}，hll02={2,4,4,4,4,6,8,9}
pfadd hll01 1 3 5 7 9
# 结果：(integer) 1
pfadd hll02 2 4 4 4 4 6 8 9
# 结果：(integer) 1

# 统计基数
pfcount hll02
# 结果：(integer) 5

# 统计基数
pfcount hll01 hll02
# 结果：(integer) 9

# 合并
pfmerge hll03 hll01 hll02
# 结果：OK
pfcount hll03
# 结果：(integer) 9
~~~



#### 3.9.3 使用场景

- 统计某个网站的UV(独立访客)：即同一个ip多次登录只算一个用户
- 用户搜索网站关键词的数量
- 统计用户每天搜索不同词条个数



### 3.10 GEO







## 4、持久化

### 4.1 概述

- 作用：把内存中的数据写入到磁盘中
- 为什么要持久化：因为redis是一种内存数据库，在运行使用过程中，数据是在内存中的，内存是断电就会消失，那么此时redis的数据也会消失。为了解决这种问题，需要重启数据就能恢复，就需要持久化，从磁盘中读回数据
- 持久化方式有两种
  - RDB：通俗来说就是把内存数据文件整个保存，要用直接读取就行（默认）
  - AOF：通俗来说就是把所有数据写命令记录下来，要用的时候一个个按照命令执行

![总结](.\图片\持久化\持久化.png)



### 4.2 RDB

#### 4.2.1 概念

- 是什么

  - **RDB持久性以指定的时间间隔执行数据集的时间点快照**

  - 实现类似照片记录效果的方式，<font color="red">**就是把某一时刻的数据和状态以文件的形式写到磁盘上**</font>，也就是快照。这样一来即使故障宕机，快照文件也不会丢失，数据的可靠性也就得到了保障。

  - 这个快照文件成为RDB文件(dump.rdb)，其中，RDB也就是Redis Database的缩写

- 能干嘛

  - 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot内存快照，它恢复时再将硬盘快照文件直接读会到内存中
  - 一锅端：Redis的数据都在内存中，保存备份时它执行的是<font color="red">**全量快照**</font>，也就是说，把内存中的所有数据记录到磁盘中

- RDB保存的是dump.rdb文件



#### 4.2.2 自动触发机制

##### 4.2.2.1 时间间隔配置

- 配置格式：save seconds times
  - seconds：时间间隔
  - times：修改次数
  - 即：表示m秒内数据集存在n次修改时，自动触发bgsave

- 6.0.16版本以下的配置文件
  - 自动触发：在Redis.conf配置文件中的SNAPSHOTTING片段下配置save参数，来触发Redis的RDB持久化条件
  - 比如：save m n：表示m秒内数据集存在n次修改时，自动触发bgsave
    - save 900 1：每隔900s（15min），如果有超过1个key发生了变化，就写一份新的RDB文件
    - save 300 10：每隔300s（5min），如果有超过10个key发生了变化，就写一份新的RDB文件、
    - save 60 10000：每隔60s（1min），如果有超过10000个key发生了变化，就写一份新的RDB文件

![总结](.\图片\持久化\rdb6.0.16版本以下配置文件.png)

- 6.2版本和7.0版本配置文件
  - 比如：save m n：表示m秒内数据集存在n次修改时，自动触发bgsave
    - save 3600 1：每隔3600s（1hour），如果有超过1个key发生了变化，就写一份新的RDB文件
    - save 300 100：每隔300s（5min），如果有超过100个key发生了变化，就写一份新的RDB文件、
    - save 60 10000：每隔60s（1min），如果有超过10000个key发生了变化，就写一份新的RDB文件

![总结](.\图片\持久化\rdb6.2版本以上配置文件.png)

- 总结：<font color="red">**只是时间刻度发生变化，设置公式还是一致**</font>



##### 4.2.2.2 配置文件之dump文件

- 默认存放地址修改为：dir ./myredis/dumpfiles/
  - 此文件路径需要提前建好：mkdir dumpfiles

![总结](.\图片\持久化\dump文件默认存放地址.png)

- dump文件默认文件名修改为：dbfilename dump6379.rdb

![总结](.\图片\持久化\dump文件默认文件名.png)

- 修改触发机制：save 5 2
- 重启服务：
  - shutdown
  - redis-server myredis/redis7.conf
- 脸上客户端：redis-cli -a li998813 -p 6379
- **读取配置看是否生效**
  - config get requirepass：获取密码
  - config get dir：获取rdb文件存放地址

- 测试
  - 5s内输入两次操作命令：会在指定目录下生成121Bytes的dump6379.rdb文件
  - 超过5s内输入两次操作命令：dump6379.rdb文件变成了128Bytes

- 原因：<font color="red">**两种情况可以直接合并为一个：若自上次rdb文件保存后，时间超过5秒，且在此期间累计的修改次数>=2次，就会触发备份**</font>。每5s检查一次，检查点如果没有两个key就不写，达到两个立即写，5s最多写一次

  - 在题目描述的情况下，RDB 设置了每 5 秒进行一次快照，但是如果在 5 秒内修改次数超过了 2 次，也会进行快照。**这是因为在 Redis 中，保存快照并不是在规定的时间到达后才进行，而是在修改数据时和时间间隔条件的双重限制下才进行的。**
  - 如果限制只按时间间隔来进行保存快照，则会出现两个问题：
    - 如果时间间隔太大，那么 Redis 持久化的数据可能会丢失，并且故障恢复时的数据可能会受到影响。
    - 如果时间间隔太小，那么数据的保存成本就会过高，并可能导致 Redis 运行效率下降。

  - 因此，**Redis 引入了按时间和数据修改次数双重限制的快照保存机制，以在灵活性和效率之间取得平衡**。如果在 5 秒内修改的次数超过 2 次，则说明数据的变化较快，在此情况下保存快照并不会带来明显的性能问题。因此，Redis 将其纳入保存快照的范围，以保证数据的安全和一致性	

- 总结：<font color="red">**每5s检查够不够2次操作**</font>font>



##### 4.2.2.3 如何恢复数据

- 案例步骤：
  - 将原本的dump6379.rdb更名为dump6379.rdb.bak
  - flushdb后将新生成的dump6379.rdb删除
  - 再将dump6379.rdb.bak改名为dump6379.rdb
  - 重启服务重新检查

- <font color="red">**将备份文件(dump.rdb)移动到 Redis 安装目录并启动服务即可**</font>
- 备份成功后故意用flushdb清空redis，看看是否可以恢复数据
  - 执行flushall/flushdb命令也会产生dump.rdb文件，但里面是空的，无意义

- <font color="red">**物理恢复，一定要将服务产生的RDB文件备份一份，然后分机隔离，避免生产上物理损坏后备份文件也挂了。**</font>



#### 4.2.3 手动触发

- <font color="red">**redis提供了两个命令来生成RDB文件，分别是save和bgsave**</font>
- **save**：在主程序中执行会**阻塞**当前redis服务器，直到持久化工作完成执行save命令期间，Redis不能处理其他命令，<font color="red">**线上禁止使用**</font>

- **bgsave(默认)**：redis会在后台异步进行快照操作，**不阻塞**快照同时还可以相应客户端请求，该触发方式会fork一个子进程由子进程复制持久化过程
  - Redis会使用bgsave对当前内存中的所有数据做快照，<font color="red">**这个操作是子进程在后台完成的，这就允许主进程同时可以修改数据**。</font>
- LASTSAVE：可以通过lastsave命令获取最后一次成功执行快照的时间

~~~bash
# 执行一次命令
set k5 v5
# 手动触发save
save
# 结果：会将数据写入到dump.rdb
lastsave
# 结果：(integer) 1749998208

# 再写一次命令
set k6 v6
# 手动触发bgsave
bgsave
# 结果：会将数据写入到dump.rdb
lastsave
# 结果：(integer) 1749998246
~~~

![总结](.\图片\持久化\rdb之手动触发.png)



#### 4.2.4 优缺点

- 优点
  - 适合大规模的数据恢复
  - 按照业务定时备份
  - 对数据完整性和一致性要求不高
  - rdb文件在内存中的加载速度比aof高很多
- 缺点
  - 在一定间隔时间做一次备份，如果redis意外down掉的话，就会丢失从当前至最近一次快照期间的数据，<font color="red">**快照之间的数据会丢失**</font>
  - 内存数据的全量同步，如果数据两太大会导致I/O严重影响服务器性能
  - RDB依赖于主进程的fork，在更大的数据集中，这可能会导致服务器请求的瞬间延迟。fork的时候内存中的数据被克隆了一份，大致2倍的膨胀性，需要考虑



#### 4.2.5 模拟数据丢失场景

~~~bash
# 操作命令
set k1 v1
set k2 v2
set k3 v3
# 这个时候只会在2的时候触发自动备份，3不会触发，因为还没达到次数

# kill -9模拟服务器宕机
kill -9 30293

# 重启服务
redis-server myredis/redis7.conf

# 检查多少个key
keys *
# 结果
1) "k1"
2) "k2"
~~~



#### 4.2.6 检查修复rdb文件

- 进入到redis安装目录，执行redis-check-rdb命令 
- redis-check-rdb rdb文件路径

~~~bash
# 进入到redis的安装目录
cd /usr/local/bin/
# 启动检查修复命令
redis-check-rdb /opt/redis-7.4.0/myredis/dumpfiles/dump6379.rdb
~~~

![总结](.\图片\持久化\检查修复rdb文件.png)



#### 4.2.7 那些情况会发生rdb快照

- 配置文件中默认的快照配置
- 手动save/bgsave命令
- 执行flushdb/fulshall命令也会产生dump.rdb文件，但是也会将命令记录到dump.rdb文件中，恢复后依旧是空，无意义
- 执行shutdown且没有设置开启AOF持久化
- 主从复制时，主节点自动触发



#### 4.2.8 如何禁用快照

- 动态所有停止RDB保存规则的方法：redis-cli config set value ""
- 手动修改配置文件：把这个配置打开

![总结](.\图片\持久化\禁用RDB快照.png)



#### 4.2.9 RDB优化配置项详解

- save <seconds> <changes>：配置快照保存条件
- dir：配置快照保存目录地址
- dbfilename：配置快照的文件名
- stop-writes-on-bgsave-error：默认yes。如果配置成no，表示不在乎数据不一致或者有其他的手段发现和控制这种不一致，**那么在快照写入失败时，也能确保redis继续接受新的写请求**

![总结](.\图片\持久化\stop-writes-on-bgsave-error.jpg)

- rdbcompression：默认yes，对于存储到磁盘中的快照，可以设置是否进行**压缩存储**。如果是的话，Redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能

![总结](.\图片\持久化\rdbcompression.jpg)

- rdbchecksum：默认yes，在存储快照后，**还可以让redis使用CRC64算法来进行数据校验**，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

![总结](.\图片\持久化\rdbchecksum.jpg)

- rdb-del-sync-files：**在没有持久化的情况下删除复制中使用的RDB文件**。默认情况下no，此选项是禁用的。

![总结](.\图片\持久化\rdb-del-sync-files.jpg)



#### 4.2.10 总结

![总结](.\图片\持久化\rdb总结.png)



### 4.3 AOF

#### 4.3.1 概念

- 是什么：<font color="red">**以日志的形式来记录每个写操作**</font>，将redis执行过的所有写指令记录下来（读操作不记录），只需追加文件但不可改写文件，redis启动之初会读取该文件重新构建数据，换言之，<font color="red">**redis重启的话就会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作**</font>
- 默认情况下，redis是没有开启AOF（append only file）的
- 能干嘛：解决RDB持久化导致的数据丢失情况
- 开启AOF功能需要设置配置：<font color="red">**appendonly yes**</font>
- AOF保存的是appendonly.aof文件



#### 4.3.2 AOF持久化工作流程

![总结](.\图片\持久化\AOF持久化工作流程.png)

1. Client作为命令的来源，会有多个源头以及源源不断的请求命令
2. 在这些命令到达Redis Server以后并不是直接写入AOF文件，会将这些命令先放入AOF缓存区中进行保存。**这里的AOF缓存区实际上是内存的一片区域，存在的目的是当这些命令达到一定量以后再写入磁盘，避免频繁的磁盘IO操作**
3. AOF缓冲会根据AOF缓存区<font color="red">**同步文件的三种写回策略**</font>将命令写入磁盘的AOF文件
4. 随着写入AOF文件的内容的增加为避免文件膨胀，会根据规则进行命令的合并（又称<font color="red">**AOF重写**</font>），从而起到AOF文件压缩的目的
5. 当Redis Server服务器重启的时候会根据AOF文件载入数据



#### 4.3.3 三种写回策略

- always：同步写回，每个写命令执行完立即同步的将日志写回磁盘AOF文件
- everysec（默认）：每秒写回，每个写命令执行完，只是先把日志写到AOF文件的内存缓冲区，每隔1s把缓冲区的内容写入到磁盘AOF文件
- no：操作系统控制写回，每个命令执行完，只是先把日志写到AOF文件的内存缓冲区，由操作系统决定何时将缓冲区内容写回磁盘

|  配置项  |     写回时机     |           优点           |               缺点               |
| :------: | :--------------: | :----------------------: | :------------------------------: |
|  always  |     同步写回     | 可靠性高，数据基本不丢失 | 每个写命令都要落盘，性能影响较大 |
| everysec |     每秒写回     |         性能适中         |        宕机时丢失1s内数据        |
|    no    | 操作心痛控制写回 |          性能好          |        宕机时丢失数据较多        |



#### 4.3.4 配置文件

- aof默认关闭，如何**开启：appendonly yes**
- 使用默认的写回策略：**appendfsync everysec**
- aof文件保存路径
  - redis6：aof保存文件的位置和rdb保存文件的位置是一样的，都是通过redis.conf配置文件的dir配置
  - redis7：<font color="red">**dir + appenddirname** </font>这两个形成的最终路径，比如：dir ./myredis，appenddirname "appendonlydir"，则最终路径：./myredis/appendonlydir

- 文件保存名称
  - redis6：有且仅有一个，appendonlydir
  - redis7：appendfilename "appendonly.aof"，<font color="red">**采用Multi Part AOF的设计** </font>，就是将原来的单个AOF文件拆分成多个AOF文件，分别是
    - **BASE：基础AOF**，一般由子进程通过重写产生，最多只有一个
    - **INCR：增量AOF**，一般会在AOFRW(重写)开始执行时被创建，该文件可能存在多个
    - **HISTORY：历史AOF**，由BASE和INCR变化而来，每次AOFRW(重写)成功完成时，本次AOFRW(重写)之前对应的BASE和INCR都会变成HISTORY，HISTORY类型的AOF会被redis自动删除
    - **为了管理这些AOF文件，引入了一个manifest（清单）文件来跟踪，管理这些AOF**。同时，<font color="red">**为了便于AOF备份和拷贝，将所有的AOF和manifest文件放入一个单独的文件目录中，目录由appenddirname来配置**</font>，目录下文件：
      - 基本文件：appendonly.aof.1.base.aof
      - 增量文件：appendonly.aof.1.incr.aof、appendonly.aof.2.incr.aof ...
      - 清单文件：appendonly.aof.manifest

![总结](.\图片\持久化\aof文件.png)



#### 4.3.5 恢复数据案例

- set了三个值之后，会在配置的dir目录下生成dump6379.rdb文件和aof的文件目录

![总结](.\图片\持久化\持久化文件夹.png)

- 删除dump6379.rdb并将aof文件夹改名为appendonlydirbak
- flushdb清空redis数据库
- 关闭redis服务，然后再重启服务，发现没有key值
- 然后将appendonlydirbak改回appendonlydir重启服务，发现有key值
- 注意：
  - **后续set等命令只会写进incr文件**
  - **get命令不会写进aof文件中**



#### 4.3.6 aof文件异常处理

- 现象：每秒钟都会写入到aof文件中，但是如果因为网络波动问题导致内容才写一半，redis挂了，那么就会出现aof文件异常
- 模拟：往incr文件中乱写一段模拟网络闪断文件写error

![总结](.\图片\持久化\aof文件错误.png)

- 重启服务，发现无法启动

![总结](.\图片\持久化\aof文件错误无法启动服务.png)

- <font color="red">**异常修复命令：redis-check-aof --fix appendonly.aof.1.incr.aof**</font>

![总结](.\图片\持久化\aof修改成功.png)

- 修改成功后的文件内容如下：删除了多余错误的文件内容

![总结](.\图片\持久化\修改成功后的文件.png)



#### 4.3.7 优缺点

- 优点：<font color="red">**更好的保护数据不丢失、性能高、可做紧急恢复**</font>
  - **使用AOF更加持久**：可以有不同的fsync策略：根本不fsync、每秒fsync、每次查询时fsync。使用每秒fsync的默认策略，写入性能仍然很棒。fsync是使用后台线程执行的，当没有fsync正在进行时，主线程将努力执行写入，因此只会丢失一秒钟的写入
  - **AOF日志时一个仅附加日志，因此不会出现寻道问题，也不会在断电时出现损坏问题**，即便某种原因（磁盘已满或者其他原因）日志写一半的命令结尾，redis-check-aof工具也能修复
  - 当AOF变得太大时，redis能够在后台自动重写AOF。重写是完全安全的，因此当redis继续附加到旧文件时，会使用创建当前数据集所需的最少操作集生成一个全新的文件，一旦第二个文件准备就绪，redis就会切换两者并开放式附加到新的那一个
  - AOF以易于理解和解析的格式依次包含所有操作的日志，**甚至可以轻松导出并修改AOF文件**。即便不小心flushdb刷新了所有内容，只要在此期间没有执行日志重写，可以通过停止服务器、删除最新命令重启来恢复数据

- 缺点：
  - **相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb**
  - **aof运行效率要慢于rdb**，每秒同步策略效率较好，不同步效率和rdb相同（即每次命令直接写入aof文件，不缓存）



#### 4.3.8 aof重写机制

- <font color="red">**启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集**</font>
  - 例子：现在有3条命令，set k1 v1、set k1 v2、set k1 v3，如果不重写，那么这三条命令都会保存在aof文件中，但是实际上恢复数据只需要set k1 v3这一条命令即可，所以，开启重写后就会只保留set k1 v3这一条命令，相当于给aof文件瘦身减肥，性能更好
- <font color="red">**aof重写不仅降低了文件的占用空间，同时更小的aof文件也可以更快的被redis加载**</font>
- 触发机制
  - 自动触发：满足配置文件中的选项后，redis会记录上次重写的aof文件大小，<font color="red">**默认配置是当aof文件大小是上次rewrite后大小的一倍且文件大于64M时触发**</font>
    - auto-aof-rewrite-percentage 100：超过上次rewrite后大小的百分之多少
    - auto-aof-rewrite-min-size 64mb：此时文件大小
    - <font color="red">**同时满足，且的关系**</font>
  - 手动触发：客户端向服务器发送bgrewriteaof指令

- 自动触发案例步骤

  - 开启aof
  - 重写峰值文件大小为1k：auto-aof-rewrite-min-size 1kb
  - 关闭aof+rdb混合，设置为no：aof-use-rdb-preamble no
  - 删除之前的rdb和aof文件，避免混淆
  - set k1 v1、set k1 v2、set k1 v3 ...一直这样重写k1的值，最终观察aof文件

  - 案例结果：
    - 当文件大小达到1kb时，会生成新的base和incr文件，而base中会保留最小指令集，新的命令则写入incr文件中

![总结](.\图片\持久化\重写.png)

- 手动触发案例
  - bgrewriteaof

![总结](.\图片\持久化\手动触发重写.png)

- 结论：<font color="red">**AOF文件重写不是对原文件进行重新整理，而是直接读取服务器现有的键值对，然后用一条命令去代替之前记录的这个键值对的多条命令，生成一个新的文件后去替换原来的AOF文件**</font>
  - AOF文件重写触发机制：通过redis,conf配置文件中的auto-aof-rewrite-percentage 100，以及auto-aof-rewrite-min-size 64mb配置，也就是说默认情况下，<font color="red">**当AOF文件大小是上次rewrite后大小的一倍且文件大于64MB时触发**</font>

- 重写原理
  - 1：在重写开始前，redis 会创建一个 “重写子进程”，这个子进程会读取现有的 AOF 文件，并将其包含的指令进行分析压缩并写入到一个临时文件中。
  - 2：与此同时，主进程会将新接收到的写指令一边累积到内存缓冲区中，一边继续写入到原有的 AOF 文件中，这样做是保证原有的 AOF 文件的可用性，避免在重写过程中出现意外。
  - 3：当 “重写子进程” 完成重写工作后，它会给父进程发一个信号，父进程收到信号后就会将内存中缓存的写指令追加到新 AOF 文件中
  - 4：当追加结束后，redis 就会用新 AOF 文件来代替旧 AOF 文件，之后再有新的写指令，就都会追加到新的 AOF 文件中
  - 5：**重写 aof 文件的操作，并没有读取旧的 aof 文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的 aof 文件**，这点和快照有点类似



#### 4.3.9 配置总结

| 配置指令                                                    | 配置含义                   | 配置示例                                                     |
| ----------------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| appendonly                                                  | 是否开启 aof               | appendonly yes                                               |
| appendfilename                                              | 文件名称                   | appendfilename "appendonly.aof"                              |
| appendfsync                                                 | 同步方式                   | everysec/always/no                                           |
| no-appendfsync-on-rewrite                                   | aof 重写期间是否同步       | no-appendfsync-on-rewrite no                                 |
| auto-aof-rewrite-percentage <br />auto-aof-rewrite-min-size | 重写触发配置、文件重写策略 | auto-aof-rewrite-percentage 100 <br />auto-aof-rewrite-min-size 64MB |



### 4.4 RBD和AOF

- 同时开启rdb和aof时，重启时只会加载aof文件，不会加载rdb文件

![总结](.\图片\持久化\加载顺序.png)

- **官方建议两者配合使用：aof-use-rdb-preamble yes**
- 混合方式：**RDB做全量持久化，AOF做增量持久化**
  - 先使用RDB进行快照存储，然后使用AOF持久化记录所有的写操作，当重写策略满足或手动触发重写的时候，<font color="red">**将最新的数据存储为新的rdb记录**</font>。这样重启的话，服务会从rdb和aof两部分恢复数据，既保证了数据完整性，又提高了恢复数据的性能。
  - 简单来说，混合方式产生的文件一部分时rdb格式，一部分时aof格式。<font color="red">**AOF包括了RDB头部+AOF混写**</font>



### 4.5 纯缓存模式

- 如果只想提高缓存效率，不想有持久化分散性能，可以禁用rdb和aof
- 禁用rdb：save ""，但是仍然可以用save、bgsave生成rdb文件
- 禁用aof：appendonly no，但是仍然可以用bgrewriteaof生成aof文件



## 5、redis事务

### 5.1 基本概念

- 是什么：可以一次性执行多个命令，本质是一组命令的集合。开启事务后，会把所有的命令加入到一个队列中，在这个事务中的所有命令都会序列化，<font color="red">**按顺序的串行化执行而不会被其他命令插入，不许加塞**</font>

- 能干嘛：一个队列中，一次性、顺序性、排他性的执行一系列命令
- Redis事务 VS 数据库事务

|        不同        |                             解释                             |
| :----------------: | :----------------------------------------------------------: |
|   单独的隔离操作   | Redis 的事务仅仅是保证事务里的操作会被连续独占的执行，<font color="red">**redis 命令执行是单线程架构，在执行完事务内所有指令前是不可能再去同时执行其他客户端的请求的**</font> |
| 没有隔离级别的概念 | 因为事务提交前任何指令都不会被实际执行，也就不存在 “事务内的查询要看到事务里的更新，在事务外查询不能看到” 这种问题了 |
|    不保证原子性    | Redis 的事务不保证原子性，也就是不保证所有指令同时成功或同时失败，<font color="red">**只有决定是否开始执行全部指令的能力，没有执行到一半进行回滚的能力**</font> |
|       排它性       |  Redis 会保证一个事务内的命令依次执行，而不会被其它命令插入  |



### 5.2 常用命令

- discard：取消事务，放弃执行事务块内的所有命令
- exec：执行事务块内的所有命令
- multi：标记一个事务块的开始
- unwatch：取消watch命令对所有key的监视
- watch key [key ...]：监视一个或多个key，如果在事务执行之前这些key被其他命令所改动，那么事务将被打断



### 5.3 案例

#### 5.3.1 正常执行案例

~~~bash
127.0.0.1:6379> multi				# 开启事务
OK
127.0.0.1:6379(TX)> set k1 v1		# 设置命令
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 0
QUEUED
127.0.0.1:6379(TX)> incr k3
QUEUED
127.0.0.1:6379(TX)> exec			# 执行事务
1) OK
2) OK
3) OK
4) (integer) 1
# 成功响应

# 检验结果：值已变
127.0.0.1:6379> get k1
"v1"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"1"
~~~



#### 5.3.2 放弃事务案例

~~~bash
127.0.0.1:6379> multi				# 开启事务
OK
127.0.0.1:6379(TX)> set k1 v11		# 设置命令	
QUEUED
127.0.0.1:6379(TX)> set k2 v22
QUEUED
127.0.0.1:6379(TX)> incr k3
QUEUED
127.0.0.1:6379(TX)> discard			# 放弃取消事务
OK

# 检验结果：值没变
127.0.0.1:6379> get k1
"v1"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"1"
~~~



#### 5.3.3 全体连坐

- 一个语法出错，全体连坐。**如果任何一个命令语法有错，redis直接返回错误，所有的命令都不执行**

~~~bash
127.0.0.1:6379> multi									# 开启事务
OK
127.0.0.1:6379(TX)> set k1 v11							# 设置命令
QUEUED
127.0.0.1:6379(TX)> set k2 v22
QUEUED
127.0.0.1:6379(TX)> set k3								# 设置错误命令，会返回有错误
(error) ERR wrong number of arguments for 'set' command
127.0.0.1:6379(TX)> exec								# 执行说全部discard了
(error) EXECABORT Transaction discarded because of previous errors.

# 检验结果：值没变
127.0.0.1:6379> get k1
"v1"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"1"
~~~



#### 5.3.4 冤有头债有主

- 前期语法编译是没错的，但是在exec执行后报错，这个时候冤有头债有主，对的执行错的停
- 注意：<font color="red">**和传统数据库事务不同，不一定要全部成功或者全部失败**</font>

~~~bash
127.0.0.1:6379> multi						# 开启事务
OK
127.0.0.1:6379(TX)> set k1 v11				# 设置命令
QUEUED
127.0.0.1:6379(TX)> incr k2 				# k2不是数字，所以incr是不对的，但是语法上没错，所以暂时不会报错
QUEUED
127.0.0.1:6379(TX)> set k3 v33
QUEUED
127.0.0.1:6379(TX)> exec				    # 执行时发现incr k2不对，这个时候1和3执行成功，2失败
1) OK
2) (error) ERR value is not an integer or out of range
3) OK

# 检验结果：k1和k3都变了，k2没变
127.0.0.1:6379> get k1
"v11"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"v33"
~~~



#### 5.3.5 watch监控

- 悲观锁：顾名思义很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会被block直到它拿到锁
- 乐观锁：顾名思义很乐观，每次去拿数据的时候都认为被人不会修改，**所以不会上锁**，但是在更新的时候会判断以下在此期间别人有没有去更新这个数据。<font color="red">**提交策略：提交版本必须大于记录当前版本才能执行更新**</font>
- <font color="red">**redis使用watch来提供乐观锁定，类似于cas**</font>
  - 一旦执行了exec之前加的监控锁都会被取消掉
  - 当客户端连接丢失的时候（比如退出连接），所有的东西都会被取消监视
- 案例1：成功的

~~~bash
127.0.0.1:6379> set name lzy			# 设置两个值
OK
127.0.0.1:6379> set balance 100
OK
127.0.0.1:6379> watch balance			# 监视balance这个字段
OK
127.0.0.1:6379> multi					# 开启事务
OK
127.0.0.1:6379(TX)> set name lzy1
QUEUED
127.0.0.1:6379(TX)> set balance 110
QUEUED
127.0.0.1:6379(TX)> exec			    # 因为在此期间没有其他客户端修改，所以可以正常执行
1) OK
2) OK

# 检验结果：值变化了
127.0.0.1:6379> get name
"lzy1"
127.0.0.1:6379> get balance
"110"
~~~

- 案例2：失败的

~~~bash
# 客户端1
127.0.0.1:6379> watch balance				# 监视balance
OK
127.0.0.1:6379> multi						# 开启事务，这个之后客户端2会修改balance的值
OK
127.0.0.1:6379(TX)> set name lzy2
QUEUED
127.0.0.1:6379(TX)> set balance 150
QUEUED
127.0.0.1:6379(TX)> exec					# 执行发现balance已经被修改了，所以会全部失败，返回nil
(nil)

# 检验结果：没有生效
127.0.0.1:6379> get name
"lzy1"
127.0.0.1:6379> get balance
"120"
~~~

- 案例3：放弃监控

~~~bash
# 客户端1
127.0.0.1:6379> unwatch						# 取消所有key的监控
OK
127.0.0.1:6379> multi						# 开启事务，这个之后客户端2会修改balance的值
OK
127.0.0.1:6379(TX)> set name lzy3
QUEUED
127.0.0.1:6379(TX)> set balance 200
QUEUED
127.0.0.1:6379(TX)> exec					# 执行发现balance已经被修改了，所以会全部失败，返回nil
1) OK
2) OK

# 检验结果：没有生效
127.0.0.1:6379> get name
"lzy3"
127.0.0.1:6379> get balance
"200"
~~~



### 5.4 总结

- 开启：以mulit开启一个事务
- 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列中
- 执行：由exec命令触发事务



## 6、redis管道

### 6.1 问题由来

- Redis 是一种基于<font color="red">**客户端 - 服务端模型**</font>以及请求 / 响应协议的 TCP 服务。一个请求会遵循以下步骤:
  - 客户端向服务端发送命令分四步 (发送命令→命令排队→命令执行→返回结果)，并监听 Socket 返回，通常以<font color="red">**阻塞模式**</font>等待服务端响应。
  - 服务端处理命令，并将结果返回给客户端。
- 上述两步称为：Round Trip Time (简称 RTT, 数据包往返于两端的时间)

![总结](.\图片\管道\redis客户端与服务端的交互模型.png)

- 如果同时需要执行大量的命令，那么就要等待上一条命令应答后再执行，这中间不仅仅多了 RTT（Round Time Trip），而且还频繁调用系统 IO，发送网络请求，同时需要 redis 调用多次 read () 和 write () 系统方法，系统方法会将数据从用户态转移到内核态，这样就会对进程上下文有比较大的影响了，性能不太好



### 6.2 解决思路

- 管道（pipeline）可以一次性发送多条命令给服务端，服务端依次处理完毕后，<font color="red">**通过一条响应一次性将结果返回，通过减少客户端与redis的通信次数来实现降低往返时延时间。**</font>pipeline实现原理是队列，先进先出特性就是保证数据的顺序性

![总结](.\图片\管道\管道实现模型.png)

- 定义：Pipeline是为了解决RTT往返时间，仅仅是将命令打包一次性发送，对整个redis的执行不造成其他任何影响
- 总结：<font color="red">**批处理命令变种优化措施**</font>，类似redis的原生批命令（mget和mset）



### 6.3 案例

- 准备一个命令脚本，把所有的命令写进这个脚本里，cmd.txt内容如下

~~~bash
set k100 v100
set k200 v200
hset k300 name lzy
hset k300 age 20
hset k300 sex 男
lpush list 1 2 3 4 5
~~~

- 执行命令：<font color="red">**cat cmd.txt | redis-cli -a li998813 -p 6379 --pipe**</font>
  - 命令解释：将cat显示出来的数据作为后面执行的参数
  - --pipe：管道形式执行

~~~bash
# 管道方式执行
[root@k8s-master lzy]# cat cmd.txt | redis-cli -a li998813 -p 6379 --pipe
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 6

# 执行结果：数据成功执行
127.0.0.1:6379> get k100
"v100"
127.0.0.1:6379> get k200
"v200"
~~~



### 6.4 总结

- 与原生批量命令对比
  - 原生批量命令是原子性（如：mset、mget），<font color="red">**pipeline是非原子性的**</font>
  - 原生批量命令一次只能执行一种命令，pipeline支持批量执行不同的命令
  - 原生批量命令是服务端实现，而pipeline需要服务端和客户端共同完成
- 与事务对比
  - 事务不保证原子性，但是有，执行的时候不会被其他命令所打扰；管道不具备原子性
  - 管道一次性将多条命令发送到服务器，事务是一条一条的发，事务只有在接收到exec命令后才会执行，管道不会
  - 执行事务时会阻塞其他命令的执行，执行管道中的命令则不会
- 使用pipeline注意事项
  - pipeline缓冲的指令只是会依次执行，不保证原子性，如果执行中指令发生异常，将会继续执行后续的指令
  - 使用pipeline组装的命令个数不要太多，不然数据量过大客户端阻塞的时间可能很久，同时服务端此时也会被迫回复一个队列答复，占用很多内存



## 7、发布订阅

### 7.1 概念

- 是什么：是一种消息通信模式：发送者（PUBLIC）发送消息，订阅者（SUBSCRIBER）接收消息，可以实现进程间的消息传递
- 总结：redis可以实现消息中间件mq的功能，通过发布订阅实现消息的引导和分流
- 能干嘛： redis客户端可以订阅任意数量的频道，就像微信关注多个公众号
  - 当有新消息通过publish命令发送给频道channel1时，客户端就可以接收到

![总结](.\图片\发布订阅\发布订阅1.png)

- 发布订阅其实就是一个轻量的队列，只不过数据不会被持久化，一般用来处理**实时性较高的异步消息**

![总结](.\图片\发布订阅\发布订阅2.png)



### 7.2 常用命令

- psubscribe pattern [pattern ...]：订阅一个或多个符合给定模式的频道







## 8、主从复制

### 8.1 概念

- 主从复制：<font color="red">**master以写为主，slave以读为主**</font>
- <font color="red">**当master数据变化的时候，自动将新的数据异步同步到其他slave数据库**</font>

![总结](.\图片\主从复制\基本架构.png)

- 能干嘛
  - 读写分离
  - 容灾恢复
  - 数据备份
  - 水平扩容支撑高并发
- 怎么玩：配从库不配主库
  - master如果配置了requirepass参数，需要密码登录
  - 那么slave就需要配置masterauth来设置校验密码，否则的话master会拒绝slave的访问请求
- 基本操作命令
  - info replication：可以查看复制节点的主从关系和配置信息
  - replicaof 主库IP 主库端口：要配成谁的从机（拜谁的码头），一般写进redis.conf配置文件
  - slaveof 主库IP 主库端口：（拜谁的码头命令版，上面的是配置版）
    - 每次与master断开之后，都需要重新连接，除非配置进了redis.conf文件
    - 在运行期间修改slave节点的信息，如果该数据库已经是某个主数据库的从数据库，那么会停止与原主数据库的同步关系<font color="red">**转而和新的主数据库同步，重新拜码头**</font>
  - slaveof no one：是当前数据库停止与其他数据库的同步，<font color="red">**转为主数据库，自立为王**</font>



8.2 
