

# Redis

## 1.NoSQL

not only sql  泛指非关系型数据库

特点：

- 易扩展（数据之间没有关系）
- 大数据量，高性能
- 多样灵活的数据模型



传统的RDMS和NoSQL

```
传统的RDMS
	-结构化组织
	-SQL
	-数据和关系都存在单独的表中
	-数据操作语言
	-数据定义语言
	-严格的一致性
Nosql:
	-不仅仅是数据
	-没有固定的查询语言
	-键值对存储，列存储，文档存储，图形数据库
	-最终一致性
	-CAP定理 和 BASE
	-高性能，高可用，高可扩展
```



## 2.Redis入门

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。



Redis能干嘛？

- 内存存储，持久化（rdb,aof）
- 效率高，用于高速缓存
- 发布订阅系统
- 计时器
- ..........



> 特性

- 多样的类型
- 持久化
- 集群
- 事务



## 3.Linux安装Redis

1. 在opt目录下创建redis文件夹

2. 远程文件传输

   ![image-20210918165947445](F:\笔记图片\img\image-20210918165947445.png)

3. 解压压缩包

4. 进入解压后的文件，可以看到redis的配置文件

   ![image-20210918170057730](F:\笔记图片\img\image-20210918170057730.png)

5. 基本环境安装

   ```
   yum install gcc-c++
   
   make
   
   make install
   ```

6. 默认安装路径 usr/local/bin

   ![image-20210918170633594](F:\笔记图片\img\image-20210918170633594.png)

7. ![image-20210918171016358](F:\笔记图片\img\image-20210918171016358.png)

8. ![](F:\笔记图片\img\image-20210918171154376.png)

9. 启动redis服务

   ![image-20210918171450187](F:\笔记图片\img\image-20210918171450187.png)

10. ![image-20210918171541158](F:\笔记图片\img\image-20210918171541158.png)

    启动成功！

11. ![image-20210918171738482](F:\笔记图片\img\image-20210918171738482.png)

12. 如何关闭redis服务

    shutdown



## 4.Redis基础知识

redis默认有十六个数据库

默认使用的是第0个，可以使用select进行切换

```bash
127.0.0.1:6379> dbsize    #查看数据库大小
(integer) 1
127.0.0.1:6379> select 1  #切换数据库
OK
127.0.0.1:6379[1]> keys *   #查看当前数据库所有的key

```

```bash
127.0.0.1:6379> keys *   
1) "name"
127.0.0.1:6379> flushdb    #清空当前数据库
127.0.0.1:6379>	flushall   #清空全部数据库内容
OK
127.0.0.1:6379> keys *
127.0.0.1:6379> exists xxx   #判断xxx是否存在
(empty array)
127.0.0.1:6379> 
```





> Redis是单线程的

Redis基于内存操作，Redis的瓶颈是根据机器内存和网络带宽

**Redis为什么这么快？**

​	误区一：高性能的服务器一定是多线程的？

​	误区二：多线程(CPU上下文切换)一定比单线程高?

核心：redis将所有的数据全部放在内存中，所以说使用单线程效率就是最高的，多线程CPU会上下文切换，这是一个耗时操作，对于内存来说，没有上下文切换效率就是最高的！

![image-20210918215122471](F:\笔记图片\img\image-20210918215122471.png)



## 5.Redis五大数据类型

![image-20210918202143181](F:\笔记图片\img\image-20210918202143181.png)



### 5.1 Redis-Key

```bash
127.0.0.1:6379> keys *  #查看当前所有的key
1) "age"
2) "name"
127.0.0.1:6379> exists name   #查看是否存在key
(integer) 1
127.0.0.1:6379> move name 1
(integer) 1
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> set name zhengxiaoxiong   
OK
127.0.0.1:6379> get name
"zhengxiaoxiong"
127.0.0.1:6379> expire name 10   #设置过期时间，单位是秒
(integer) 1
127.0.0.1:6379> ttl name   #查看当前key的剩余时间
(integer) 1
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> keys *
1) "age"


127.0.0.1:6379> type name    #查看当前key的类型
string
127.0.0.1:6379> type age
string

```



### 5.2 String

```bash
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> get key1
"v1"
127.0.0.1:6379> append key1 hello   #拼接字符串，如果当前key不存在，就相当于set一个                                       key
(integer) 7
127.0.0.1:6379> get key1
"v1hello"

127.0.0.1:6379> strlen key1    #获取字符串的长度
(integer) 7
127.0.0.1:6379> append key1 zhengxiaoxiong  
(integer) 21
#############################################################################
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views    #自增1
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views   #自减1
(integer) 1
127.0.0.1:6379> incrby views 8   #自增任意数
(integer) 9
127.0.0.1:6379> decrby views 5   #自减任意数
(integer) 4
127.0.0.1:6379> c
##############################################################################
字符串范围
127.0.0.1:6379> clear
127.0.0.1:6379> set key1 "hello,zhengxiaoxiong"
OK
127.0.0.1:6379> get key1
"hello,zhengxiaoxiong"
127.0.0.1:6379> getrange key1 0 3    #截断字符串[0,3]
"hell"
127.0.0.1:6379> getrange key1 0 -1    #查看全部的字符串
"hello,zhengxiaoxiong"

127.0.0.1:6379> set key2 abcdefg
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> setrange key2 1 xx    #替换指定位置开始的字符串
(integer) 7
127.0.0.1:6379> get key2
"axxdefg"
127.0.0.1:6379> 

##############################################################################
#setex(set with expire)  设置过期时间
#setnx(set if not exist)  如果不存在再设置（在分布式锁中常常使用）
127.0.0.1:6379> clear
127.0.0.1:6379> setex key3 60 hello  #设置key3的为hello，60s过期
OK
127.0.0.1:6379> ttl key3
(integer) 53
127.0.0.1:6379> setnx mykey redis
(integer) 1
127.0.0.1:6379> keys *
1) "mykey"
2) "key3"
3) "key2"
4) "key1"
127.0.0.1:6379> keys *
1) "mykey"
2) "key2"
3) "key1"
127.0.0.1:6379> setnx mykey "MyAism"   #如果该key存在，则创建失败
(integer) 0
127.0.0.1:6379> get mykey
"redis"
##############################################################################
#mset
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3   #同时设置多个值
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"    
127.0.0.1:6379> mget k1 k2 k3      #同时获取多个值
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1  k4 v4
(integer) 0             #msetnx是原子性操作，要么一起成功，要么一起失败
127.0.0.1:6379> mget k1 k4
1) "v1"
2) (nil)        
##############################################################################
set user:1 {name:zhangsan,age:3}  #设置一个user:1对象 值为json字符来保存对象

#这里的key是一个巧妙地设计：user:{id}:{filed}
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 3
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "3"
127.0.0.1:6379> 
##############################################################################
127.0.0.1:6379> getset db redis   #如果不存在值，返回为null并创建新的值
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db sql     #如果存在值则返回旧的值并更新最新的值
"redis"
127.0.0.1:6379> get db
"sql"

```

String类型的使用场景：value除了是我们的字符串，还可以是我们的数字

- 计数器
- 粉丝数



### 5.3 List

所有的List命令都是用l开头的

```bash
127.0.0.1:6379> lpush list one    #将一个值或多个值插入列表的头部（左）
(integer) 1
127.0.0.1:6379> lpush list two
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> lrange list 0 -1  #获取list中的值
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> lrange list 0 1   #通过区间获取具体的值
1) "three"
2) "two"
127.0.0.1:6379> rpush list right   #将一个值或多个值插入列表的头部（右）
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
##############################################################################
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
127.0.0.1:6379> lpop list    #移除列表的第一个元素（左）
"three"
127.0.0.1:6379> rpop list    #移除列表的最后一个元素（右）
"right"
127.0.0.1:6379> lrange list 0 -1    
1) "two"
2) "one"
##############################################################################
127.0.0.1:6379> lindex list 0   #通过下标获取list中的某一个值
"two"
127.0.0.1:6379> lindex list 1
"one"

##############################################################################
127.0.0.1:6379> lpush list one   
(integer) 1
127.0.0.1:6379> lpush list two
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> llen list    #返回列表的长度
(integer) 3

##############################################################################
127.0.0.1:6379> lrem list 1 one   #移除指定的值
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
127.0.0.1:6379> lpush list three 
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
##############################################################################

trim  截断
127.0.0.1:6379> lpush mylist "hello"
(integer) 1
127.0.0.1:6379> lpush mylist "hello1"
(integer) 2
127.0.0.1:6379> lpush mylist "hello12"
(integer) 3
127.0.0.1:6379> lpush mylist "hello3"
(integer) 4
127.0.0.1:6379> ltrim mylist 1 2    #通过下标截取指定的长度
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello12"
2) "hello1"
##############################################################################
Redis Rpoplpush 命令用于移除列表的最后一个元素，并将该元素添加到另一个列表并返回。

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpoplpush mylist otherlist
"hello2"
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange otherlist 0 -1
1) "hello2"
##############################################################################
lset：将列表中指定下标的值替换为指定的值

127.0.0.1:6379> lpush list value1
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "value1"
127.0.0.1:6379> lset list 0 item
OK
127.0.0.1:6379> lrange list 0 -1
1) "item"
127.0.0.1:6379> lset list 1 element   #list不存在下标为1的，如果更新就会报错
(error) ERR index out of range
##############################################################################
linsert   #将某一个具体的值插入到列表中前面或者后面

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "world"
(integer) 2
127.0.0.1:6379> linsert mylist before "world" "other"
(integer) 3
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "other"
3) "world"
```

- 实际上是一个链表
- 在两边插入或改动值效率最高
- 队列
- 栈



### 5.4 Set

Set中的值是不能重复的

```bash
127.0.0.1:6379> sadd myset "hello"  #set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset "world"
(integer) 1
127.0.0.1:6379> sadd myset "set"   
(integer) 1
127.0.0.1:6379> sismember myset hello  #判断某一个值是否在set集合中
(integer) 1
127.0.0.1:6379> smembers myset     #查看set中所有的值
1) "set"
2) "world"
3) "hello"
##############################################################################
127.0.0.1:6379> scard myset    #获取集合中元素的个数
(integer) 3
##############################################################################
127.0.0.1:6379> srem myset "hello"   #移除set中的指定元素
(integer) 1
##############################################################################

#随机抽选元素
127.0.0.1:6379> smembers myset
1) "set"
2) "world"
127.0.0.1:6379> srandmember myset     #随机抽选出一个元素
"world"
127.0.0.1:6379> srandmember myset   
"set"
127.0.0.1:6379> srandmember myset 
"set"
127.0.0.1:6379> srandmember myset 
"world"
127.0.0.1:6379> srandmember myset 2     #随机抽选出指定个数的元素
1) "set"
2) "world"

##############################################################################
127.0.0.1:6379> smembers myset
1) "set"
2) "world"
3) "hello1"
4) "hello"
5) "hello2"
127.0.0.1:6379> spop myset    #随机删除集合中的元素
"set"
127.0.0.1:6379> spop myset
"hello2"
127.0.0.1:6379> smembers myset
1) "world"
2) "hello1"
3) "hello"

##############################################################################
127.0.0.1:6379> sadd myset hello world zhengxiaoxiong
(integer) 3
127.0.0.1:6379> sadd myset2 set2
(integer) 1
127.0.0.1:6379> smove myset myset2 zhengxiaoxiong   #移动指定的元素
(integer) 1
127.0.0.1:6379> smembers myset2
1) "zhengxiaoxiong"
2) "set2"

##############################################################################
127.0.0.1:6379> sadd myset1 a b c
(integer) 3
127.0.0.1:6379> sadd myset c d e
(integer) 3
127.0.0.1:6379> sdiff myset1 myset    #差集
1) "b"
2) "a"
127.0.0.1:6379> sinter myset1 myset    #交集  共同好友就可以这样实现
1) "c"
127.0.0.1:6379> sunion myset1 myset    #并集
1) "c"
2) "b"
3) "a"
4) "e"
5) "d"

```

用处

- 将一个用户所有关注的人放在一个set中，粉丝也放在一个set集合中
- 可以实现共同关注



### 5.5 Hash

Map集合，key-map集合，Hash本质和字符串没有太大区别，本质还是key-value

```bash
127.0.0.1:6379> hset myhash filed zhengxiaoxiong   #设置值
(integer) 1
127.0.0.1:6379> hget myhash filed
"zhengxiaoxiong"
127.0.0.1:6379> hmset myhash field1 hello field2 world   #同时设置多个值
OK
127.0.0.1:6379> hmget myhash field1 field2   #获取多个字段值
1) "hello"
2) "world"
127.0.0.1:6379> hgetall myhash  #获取全部的数据
1) "field1"
2) "hello"
3) "field2"
4) "world"
##############################################################################
127.0.0.1:6379> hdel myhash field1   #删除指定的key字段，对应的value字段也就没有了
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
127.0.0.1:6379> hlen myhash     #获取字段数量
(integer) 1
##############################################################################

127.0.0.1:6379> hexists myhash field1   #判断hash中的key是否存在
(integer) 0
127.0.0.1:6379> hexists myhash field2
(integer) 1


127.0.0.1:6379> hkeys myhash     #获取所有的field
1) "field2"
127.0.0.1:6379> hvals myhash     #获取所有的value
1) "world"
##############################################################################
hincrby key filed incerment   #指定增量
hsetnx key filed value   #如果不存在就创建

```

可以存储变更数据，尤其是用户信息的保存



### 5.6 Zset(有序集合)

在Set的基础上增加了一个值，set k1 v1     zset k1 score v1

```bash
127.0.0.1:6379> zadd myset 1 one   #添加一个值
(integer) 1
127.0.0.1:6379> zadd myset 2 two 3 three 4 four     #添加多个值
(integer) 3
127.0.0.1:6379> zrange myset 0 -1   
1) "one"
2) "two"
3) "three"
4) "four"

##############################################################################

#zrangebyscore key min max
127.0.0.1:6379> zadd salary 2500 xiaohong   #添加用户
(integer) 1
127.0.0.1:6379> zadd salary 3000 zhangsan
(integer) 1
127.0.0.1:6379> zadd salary 12000 zhengxiaoxiong
(integer) 1
127.0.0.1:6379> zrangebyscore salary
(error) ERR wrong number of arguments for 'zrangebyscore' command
127.0.0.1:6379> zrangebyscore salary -inf +inf    #按照score的大小从小到大排序
1) "xiaohong"
2) "zhangsan"
3) "zhengxiaoxiong"
127.0.0.1:6379> zrangebyscore salary -inf +inf withscores
1) "xiaohong"
2) "2500"
3) "zhangsan"
4) "3000"
5) "zhengxiaoxiong"
6) "12000"


127.0.0.1:6379> zrevrange salary 0 -1   #按照score进行降序排列
1) "zhengxiaoxiong"
2) "zhangsan"
127.0.0.1:6379> zrevrange salary 0 -1 withscores
1) "zhengxiaoxiong"
2) "12000"
3) "zhangsan"
4) "3000"
##############################################################################
127.0.0.1:6379> zrange salary 0 -1
1) "xiaohong"
2) "zhangsan"
3) "zhengxiaoxiong"
127.0.0.1:6379> zrem salary xiaohong    #移除集合中的指定的元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "zhangsan"
2) "zhengxiaoxiong"

127.0.0.1:6379> zcard salary   #获取集合中的元素的个数
(integer) 2

##############################################################################
zcount key min max  #判断区间[min,max]有多少成员数量
```

set排序

存储班级成绩，工资表排序

带权重进行判断：普通消息，重要消息

排行耪



## 6.Redis三种特殊数据类型

### 6.1 geospatial

地理位置

相关命令

> GEOADD

```BASH
#geoadd 添加位置
#两极无法添加
#我们为你一般会下载城市数据，直接通过java一次性导入
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
```

> GEOPOS

```bash
#获取指定的城市的经度和纬度
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
```

>GEODIST

两人之间的距离

单位：

- m 表示单位为米
- km 表示单位为千米
- mi 表示单位为英里
- ft 表示单位为英尺

```dash
127.0.0.1:6379> geodist china:city beijing shanghai
"1067378.7564"
127.0.0.1:6379> geodist china:city beijing shanghai km
"1067.3788"

```

>GEORADIUS

以给定的经度纬度为中心，找出某一半径内的元素

georadius key 经度  纬度 半径  单位

 

> GEORADIUSBYMEMBER

找出位于指定范围内的元素，中心点是由给定的位置元素决定

GEORADIUSBYMEMBER key 成员名 半径 单位



> GEOHASH

返回一个或多个位置元素的gohash表示

返回的字符串越相似，表示两个地方越近

geohash key 成员1 成员2



> geo底层原理

geo底层是zset，我们可以使用Zset的命令来操作geo





### 6.2 Hyperloglog

> 什么是基数？

集合中元素的个数（先去重）  如{1，3，5，7，7}  集合的基数为4

Hyperloglog是用来做基数统计的算法 

- 占用的内存是固定的，2的64次方不同元素的技术，只需要占用12kb内存

网页的UV(一个人访问一个网站多次，但还是算一个人)



```bash
#pfadd key 元素   
#pfcount key    统计元素基数数量
#pfmerge destkey  [sourcekey.....]   合并元素组到destkey
127.0.0.1:6379> pfadd mykey a b c d e f g h i j
(integer) 1
127.0.0.1:6379> pfcount mykey
(integer) 10
127.0.0.1:6379> pfadd mykey2 z d k i u y t
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 7
127.0.0.1:6379> pfmerge mykey3 mykey mykey2
OK
127.0.0.1:6379> pfcount mykey3
(integer) 14

```



### 6.3 BitMaps

> 位存储

统计用户信息，打卡。两个状态的都可以使用BitMaps

BitMaps，位图，都是操作二进制位来进行记录，就只有0和1两个状态



> 案例

```bash
#打卡记录  set bitmap offset bit
127.0.0.1:6379> setbit bitmap 0 1
(integer) 0
127.0.0.1:6379> setbit bitmap 1 0
(integer) 0
127.0.0.1:6379> setbit bitmap 2 1
(integer) 0
127.0.0.1:6379> setbit bitmap 3 1
(integer) 0
127.0.0.1:6379> setbit bitmap 4 0
(integer) 0
127.0.0.1:6379> setbit bitmap 5 1
(integer) 0
127.0.0.1:6379> setbit bitmap 6 1
(integer) 0

#查看某一天是否打卡
127.0.0.1:6379> getbit bitmap 3
(integer) 1
#统计打卡天数
127.0.0.1:6379> bitcount bitmap 
(integer) 5

```



## 7.Redis中的事务

**Redis单条命令是保证原子性的，但是Redis中的事务是不保证原子性的！**

**Redis事务没有隔离的级别的概念，所有的命令在事务中，并没有被执行，只有发起执行命令的时候才会执行**

Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化

**一次性，顺序性，排他性**执行一些列的命令



redis事务：

- 开启事务（multi）
- 命令入队
- 执行事务(exec)

> 正常执行事务

```bash
127.0.0.1:6379> multi    #开启事务
OK
127.0.0.1:6379(TX)> set key2 v1     #命令入队
QUEUED
127.0.0.1:6379(TX)> set key1 v1
QUEUED
127.0.0.1:6379(TX)> get key2
QUEUED
127.0.0.1:6379(TX)> get key1
QUEUED
127.0.0.1:6379(TX)> exec    #执行事务
1) OK
2) OK
3) "v1"
4) "v1"

```

> 放弃事务

**DISCARD**

一旦放弃事务，事务中的命令都不会被执行

![image-20210921211937102](F:\笔记图片\img\image-20210921211937102.png)

> 编译型异常（代码异常），事务中所有的命令都不会被执行





> 运行时异常，如果事务队列中存在运行时异常，那么执行命令的时候，其他命令是可以正常执行的



> 监控

**悲观锁：**

- 总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程）。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中synchronized和ReentrantLock等独占锁就是悲观锁思想的实现。

**乐观锁：**

- 总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。在Java中java.util.concurrent.atomic包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。



> 使用watch操作可以当作redis的乐观锁操作

- 提交事务时，如果value没有发生变化，则会成功执行
- 提交事务时，如果value发生变化，则事务不会成功执行
  - 如果事务执行失败，就先解锁，获取最新的值，再次监视，对比监视的值是否发生了变化



## 8.Jedis

> 什么是Jredis

官方推荐的Java连接开发工具！使用Java操作redis的中间件



> 步骤

1. 导入对应的依赖

   ```xml
   <dependencies>
       <!--导入jedis的包-->
       <dependency>
           <groupId>redis.clients</groupId>
           <artifactId>jedis</artifactId>
           <version>3.6.3</version>
       </dependency>
       <!--fastjson-->
       <dependency>
           <groupId>com.alibaba</groupId>
           <artifactId>fastjson</artifactId>
           <version>1.2.78</version>
       </dependency>
   </dependencies>
   ```

2. 编码测试
   - 连接数据库
   - 操作命令
   - 断开连接

```java
public class TestPing {
    public static void main(String[] args) {
        //1.new Jedis对象
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        //jedis的所有命令就是我们之前学习的所有指令
        String ping = jedis.ping();
        System.out.println(ping);
    }
}
```



```java
public class TextTX {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        Transaction multi = null;
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("name","zhengxiaoxiong");
        multi = jedis.multi();    //开启事务
        String s = jsonObject.toJSONString();
        jedis.watch(s);         //监控
        //开启事务
        try {
            multi.set("user1",s);
            multi.set("user2",s);
            multi.exec();
        } catch (Exception e) {
            multi.discard();    //如果失败就放弃事务
            e.printStackTrace();
        } finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close();
        }
    }
}
```



## 9.SpringBoot整合

在springboot2.x之后，原来使用的jedis被替换成了lettuce

jedis:底层采用的是直连，如果有多个线程操作的话，是不安全的，如果想要避免不安全的，使用		jedis pool连接池 ，更像BIO模式

lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况，可以减少线程数			  量，更像NIO模式



> 测试

1. 导人依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 配置连接

   ```xml
   spring.redis.host=127.0.0.1
   spring.redis.port=6379
   ```

3. 测试

   ```java
   @SpringBootTest
   class Redis02SpringbootApplicationTests {
       @Autowired
       private RedisTemplate redisTemplate;
   
       @Test
       void contextLoads() {
           //redisTemplate  操作不同的数据类型
           //opsForValue 操作字符串
           //opsForList  操作List
           //opsForSet
           //opsForZSet
           //opsForHash
   
           //除了基本的操作，我们常用的操作可以直接通过redisTemplate操作，比如基本的事务和CRUD
   
           //获得redis的连接对象
   //        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
   //        connection.flushAll();
   //        connection.flushDb();
   
           redisTemplate.opsForValue().set("user","郑小熊");
           System.out.println(redisTemplate.opsForValue().get("user"));
       }
   
   }
   ```



**关于对象的保存**

![image-20210922161129618](F:\笔记图片\img\image-20210922161129618.png)

直接传递对象会报错误，所有的对象需要序列化



我们来自己编写一个RedisTemplate

```java
@Configuration()
public class RedisConfigure {
    //这是一个固定模板，拿去就可以直接使用
    //编写我们自己的redisTemplate
    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        //我们为了自己的开发方便，一般直接使用<String,Object>
        RedisTemplate<String,Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);

        //Json序列化配置
        Jackson2JsonRedisSerializer<Object> objectJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectJackson2JsonRedisSerializer.setObjectMapper(om);

        //String的序列化配置
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value序列化方式采用jackson
        template.setValueSerializer(objectJackson2JsonRedisSerializer);
        //hash的value序列化方式采用jackson
        template.setHashValueSerializer(objectJackson2JsonRedisSerializer);
        
        template.afterPropertiesSet();
        return template;
    }
}
```



## 10.Redis.conf详解

启动的时候通过配置文件来启动

1. ![image-20210922205042831](F:\笔记图片\img\image-20210922205042831.png)

   redis配置文件对大小写不敏感

2. ![image-20210922205507039](F:\笔记图片\img\image-20210922205507039.png)

   可以把多个配置文件都配置过来

3. **网络**

   ```bash
   bind 127.0.0.1   #绑定的ip
   protected-mode yes   #保护模式
   port 6379  #端口设置
   ```

4. **通用**

   ```bash
   daemonize yes  #以守护进程的方式运行
   
   pidfile /var/run/redis.pid  #如果以后台的方式进行，我们就需要指定一个pid文件
   
   #日志级别
   # debug (a lot of information, useful for development/testing)
   # verbose (many rarely useful info, but not a mess like the debug level)
   # notice (moderately verbose, what you want in production probably)
   # warning (only very important / critical messages are logged)
   loglevel notice
   logfile ""  #日志的位置文件名
   databases 16  #默认的数据库的数量
   
   ```

5. **快照**

   持久化，在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb ,.aof文件

   redis是内存数据库，没有持久化断电即失

   ```bash
   #如果900秒内至少有一个key进行了修改，我们就进行持久化操作，就会生成dump.rdb文件
   save 900 1    
   #如果300秒内至少有10个key进行了修改，我们就进行持久化操作，就会生成dump.rdb文件
   save 300 10
   #如果60秒内至少有10000个key进行了修改，我们就进行持久化操作，就会生成dump.rdb文件
   save 60 10000
   
   #我们之后学持久化，会自己定义这个测试
   
   stop-writes-on-bgsave-error yes  #持久化出错是否还需要继续工作
   rdbcompression yes  #是否压缩rdb文件，需要消耗一些cpu资源
   rdbchecksum yes   #保存rbd文件时，进行校验
   dir ./   #rdb保存文件目录
   ```

6. **安全**

   ```bash
   requirepass foobared
   可以在这里设置redis密码
   
   127.0.0.1:6379> config get requirepass    #获取redis密码
   1) "requirepass"
   2) ""
   127.0.0.1:6379> config set requirepass "123456"   #设置redis密码
   OK
   
   ```

7. **限制**

   ```bash
   maxclients 10000   #设置能连接上redis最大客户端的数量
   maxmemory <bytes>  #redis最大内存设置
   maxmemory-policy noeviction  #内存到达上限之后的处理策略
   
   ```

8. **APPEND ONLY MODE：AOF**

   ```bash
   appendonly no   #默认不开启AOF模式的，默认使用rdb方式持久化的
   
   # appendfsync always  每次修改都会同步，速度较慢
   appendfsync everysec  #每秒执行一次sync(同步)，可能会丢失这1s的数据
   # appendfsync no      不执行同步，这个时候操作系统自己同步数据，速度最快
   ```



## 11.Redis持久化

### 11.1 RDB

> 什么是redis为什么需要持久化

redis是内存数据库，如果不将内存数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失。所以redis提供了持久化功能



> 什么是RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，在用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何I/O操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那么RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化的数据可能会丢失

rdb保存的文件是 `dump.rdb`

![image-20210923101909196](F:\笔记图片\img\image-20210923101909196.png)

都是在我们的配置文件中配置的

**触发机制** ：

- save的规则满足的情况下，会自动触发rdb规则，生成dump.rdb文件
- 执行flushall命令，也会触发rdb规则
- 退出redis也会触发rdb规则

**如何恢复rdb文件**

​	只需要将dump.rdb放在redis启动目录就可以，redis启动时就会检查dump.rdb回复其中的数据



> RDB优点

1. 适合大规模的数据恢复
2. 对数据的完整性要求不高

> RDB缺点

1. 需要一定的时间间隔进行操作，如果redis意外宕机，最后一次修改的数据就没有了
2. fork进程的时候，会占用一定的内存空间



### 11.2 AOF（Append only file）

> AOF是什么

以日志的形式来记录每个操作，将Redis执行过的所有指令记录下来（读操作不记录）,只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

AOF保存的文件时`appendonly.aof`文件

![image-20210923104213095](F:\笔记图片\img\image-20210923104213095.png)

重启redis就可以生效了！

如果aof文件有错误，这时候redis是启动不起来的，我们需要修复这个aof文件,redis给我们提供了一个工具 `redis-check-aof --fix`



> AOF的优点与缺点

**优点：**

1. 每一次修改都同步，文件的完整性会更好
2. 每秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率是最高的

**缺点：**

1. 相对于数据文件来说，aof远远大于rdb,修复的速度也比rdb慢
2. aof运行的效率慢，所以redis默认的配置就是rdb



> 重写规则

1. rewrite介绍 AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集.可以使用命令`bgrewriteaof`；
2. 重写原理 AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似；
3. 触发机制 Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发；



同时开启两种持久化方式：

- 在这种情况下，当redis重启时会优先载入AOF文件来恢复原始数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整



## 12.Redis订阅发布

Redis发布订阅是一种消息通信模式，发布者（pub）发送消息，订阅者（sub）接收消息

redis客户端可以订阅任意数量的频道



消息发送者，频道，订阅者

![img](https://img-blog.csdnimg.cn/202005041125464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODg1MDg1,size_16,color_FFFFFF,t_70)

下图展示了channel1，以及订阅这个频道的三个客户端之间的关系

![img](https://img-blog.csdnimg.cn/20200504112605149.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![img](https://img-blog.csdnimg.cn/20200504112618153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODg1MDg1,size_16,color_FFFFFF,t_70)

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | PSUBSCRIBE pattern [pattern]  订阅一个或多个符合给定模式的频道 |
| 2    | PUBSUB subcommand[argument[argument...]] 查看订阅与发布系统状态 |
| 3    | PUBLISH channel message 退订所有给定模式的频道               |
| 4    | SUBSCRIBE channel[channel] 订阅给定的一个或多个频道信息      |
| 5    | UNSUBSCRIBE[channel[channel...]]  指退订给定的频道           |



## 13.Redis主从复制

主从复制：是指将一台Redis服务器的数据，复制到其他的Redis的服务器。前者称为主节点，后者称为从节点；**数据的赋值时单向的，只能由主节点到从节点**。主节点以写为主，从节点以读为主

**主从复制，读写分离，80%的情况下都是在进行读操作！能够减缓服务器的压力**

默认情况下，每台redis服务器都是主节点

且一个主节点可以有多个从节点，但一个从节点只能有一个主节点



> 主从复制的主要作用

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余
3. 负载均衡：在主从复制的基础上，配合读写分离，可以有主节点提供写服务，由从节点提供读服务，分担服务器负载，尤其是在写少读多的场景下，通过多个节点分担读负载，可以大大提高redis服务器的并发量
4. 高可用基石：除了上述作用外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础



![image-20210923145937323](F:\笔记图片\img\image-20210923145937323.png)



### 13.1 环境配置

只配置从库，不配置从库

```bash
#
127.0.0.1:6379> info replication
# Replication
role:master   # 角色
connected_slaves:0   #从机个数为0
master_failover_state:no-failover
master_replid:ba9bbc05b26b13ee734366d62b1d79ba2c99f7e4
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

```



复制三个配置文件，修改对应的信息

1. 端口
2. pid名字
3. 日志名字
4. dump.rdb的名字

修改完毕之后启动三个redis服务

![image-20210923152344844](F:\笔记图片\img\image-20210923152344844.png)

集群环境搭建OK



### 13.2 一主二从

我们一般情况下只需要配置从机就好了

从机配置使用 **slaveof 主机地址  端口号**来配置

```bash
127.0.0.1:6380> clear
127.0.0.1:6380> slaveof 127.0.0.1 6379
OK
127.0.0.1:6380> info replication 

# Replication
role:slave                 #当前为从机
master_host:127.0.0.1      #主机地址
master_port:6379           #主机端口号
master_link_status:up
master_last_io_seconds_ago:4
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:f6ac2b1155038477e738f2f45bec5da39a1f39aa
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
```

![image-20210923153342050](F:\笔记图片\img\image-20210923153342050.png)

真实的主从配置应该在配置文件中配置，这样的话就是永久的，我们这里使用的是命令，暂时的

![image-20210923154912737](F:\笔记图片\img\image-20210923154912737.png)



**细节：**

主机可以设置值，从机不可以设置值，只能读！主机中所有的信息和数据，都会被从机保存

主机断开连接，从机依旧连接到主机，但是没有写操作。如果主机重新连接，从机依旧可以直接获取到主机写的信息

如果是使用命令行配置的从机，如果从机重启，那么该从机就会变为从机。只要变为了从机，立马就会从主机中获取值

- redis复制原理

  > master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，**在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步** 
  >
  > 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。slave第一次同步为全量复制。
  >
  >  增量复制：master继续将新的所有收集到的修改命令依次传给slave,完成同步 但是只要是重新连接master,第一次完全同步（全量复制)将被自动执行。





![image-20210923160351518](F:\笔记图片\img\image-20210923160351518.png)

这个时候也会完成主从复制

如果主机断开连接，我们可以使用 **slaveof ** 让自己变成主机！其他节点就可以手动连接到这个主机



### 13.3 哨兵模式

（自动选取老大的模式）

> 概述

主从切换技术的方法是：当主服务器宕机后，需要手动把一台服务器切换为主服务器，这就需要人工干预，费时费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑哨兵模式

谋权篡位的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转化为主库

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，他会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行多个Redis实例**

![image-20210923162215889](F:\笔记图片\img\image-20210923162215889.png)

哨兵的两个作用：

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器
- 当哨兵检测到Master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服务器，修改配置文件，让他们切换主机



**配置哨兵配置文件sentine.conf**

```bash
sentinel monitor myredis 127.0.0.1 6379 1    #哨兵名称  host port 1
#数字1代表 只要有1个哨兵认为主服务器已下线，主服务器就会判定为客观下线
```

**启动哨兵**

```bash
reids-sentinel kconfig/sentinel.conf
```

![image-20210923170336836](F:\笔记图片\img\image-20210923170336836.png)

如果master断开，这个时候就会从从机中随机选择一个服务器！（这里面有一个投票算法）

![image-20210923191540928](F:\笔记图片\img\image-20210923191540928.png)

如果此时主机回来了，只能归并到新的主机下，当作从机



> 哨兵模式的优点与缺点

**优点：**

- 哨兵集群，基于主从复制模式，所有的主从配置优点他都有
- 主从可以切换，故障可以转移，系统的可用性会更好
- 哨兵模式就是主从模式的升级

**缺点：**

- redis不好扩容，如果集群容量到达上限，在线扩容就十分麻烦
- 实现哨兵模式的配置很麻烦



## 14.Redis缓存穿透与雪崩

### 14.1 缓存穿透（查不到导致的）

![1850490166e7910e7456980cb5e6ad3c.JPEG](https://www.pianshen.com/images/140/1850490166e7910e7456980cb5e6ad3c.JPEG)

> 缓存穿透

缓存穿透的概念很简单，用户想要查询一个数据，发现redis内存数据库中没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库，这会给持久层数据库造成很大的压力，这时候也就相当于出现了缓存穿透

> 解决方案

**布隆过滤器**

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力

![image-20210923194541084](F:\笔记图片\img\image-20210923194541084.png)



**缓存空对象**

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期的时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源



但是这种方法存在两个问题：

1. 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多空值的键
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响

### 14.2 缓存击穿（查询量太大，缓存过期）

典型案例：微博服务器宕机

> 概述

这里需要注意和缓存穿透的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就能击穿缓存，直接请求到数据库，就像在一个屏障上凿开了一个洞

当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库压力瞬间过大



> 解决方案

**设置热点信息永不过期**

**加互斥锁**



### 14.3 缓存雪崩

> 概述

缓存雪崩是指缓存中数据大批量到过期时间，而查询数据量巨大，引起数据库压力过大甚至宕机。

和缓存击穿不同的是， 缓存击穿指并发查同一条数据，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。



> 解决方案

大多数系统设计者考虑用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。还有一个简单方案就时将**缓存失效时间分散开**，比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

