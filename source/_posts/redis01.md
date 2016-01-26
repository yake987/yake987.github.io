title: redis 命令学习(一)
date: 2015-11-24 20:11:07
tags: redis
original: false
---
#### String操作命令
String 是 redis 中最基础的数据类型， redis 字符串是二进制安全的，这意味着他们有一个已知的长度没有任何特殊字符终止，所以你可以存储任何东西，512兆为上限。
<!-- more -->
#### SET命令  - 添加键值

SET命令是将字符串值 value 关联到 key 。

语法格式：
```
SET key value [EX seconds] [PX milliseconds] [NX|XX]
```
 如果 key 已经持有其他值，SET就覆写旧值，无视类型。因此，对于某个原本带有生存时间（TTL）的键来说， 当SET命令成功在这个键上执行时， 这个键原有的 TTL 将被清除。
示例：添加键key1，值为"test"
```
127.0.0.1:6379> SET key1 "test"
OK
```

#### SETEX命令  - 添加值和生存时间
　　SETEX命令的作用是将值 value 关联到 key ，并将 key 的生存时间设为 seconds (以秒为单位)。如果 key 已经存在， SETEX命令将覆写旧值
语法格式：
```
SETEX key seconds value
```
示例 - 设置name的值为‘jaak’，生存时间为60秒。
```
127.0.0.1:6379> SETEX name 60 "jaak"
OK
```
SETEX命令的作用类似如下两个命令：
```
SET name "jaak"
EXPIRE name 60  # 设置生存时间
```
不同之处是，SETEX是一个原子性(atomic)操作， 关联值 和 设置生存时间 两个动作会在同一时间内完成，该命令在 Redis 用作缓存时，非常实用。

#### GET命令 - 获取字符串
　　GET命令是返回 key 所关联的字符串值。如果 key 不存在那么返回特殊值 nil 。假如 key 储存的值不是字符串类型，返回一个错误，因为GET只能用于处理字符串值
语法格式：
```
GET key
```
示例 - 获取name和key的值。
```
127.0.0.1:6379> GET name
"jaak"
127.0.0.1:6379> GET key
(nil)
返回值
```
当key不存在时，返回nil，否则返回key的值。
如果key的值不是字符串类型，那么将会返回一个错误。

#### APPEND命令 - 追加字符串
如果 key 已经存在并且是一个字符串，APPEND命令将 value 追加到 key 原来的值的末尾，
语法格式：
```
APPEND key value
```
示例 - 向name追加字符‘ zhang’。

```
127.0.0.1:6379> APPEND name " zhang"    # 对已存在的字符串进行 APPEND
(integer) 10
127.0.0.1:6379> GET name
“jaak zhang"
```
如果 key 不存在，APPEND就简单地将给定 key 设为 value ，就像执行 SET key value 一样。

#### MSET命令 - 添加多个键值
　　MSET命令可以同时设置一个或多个 key-value 对，如果某个给定 key 已经存在，那么MSET会用新值覆盖原来的旧值
语法格式：
```
MSET key value [key value ...]
```
示例 - 设置name、age和number的值。

127.0.0.1:6379> MSET name "jaak" age "24" number "158234232"
OK
MSET是一个原子性(atomic)操作，所有给定 key 都会在同一时间内被设置，某些给定 key 被更新而另一些给定 key 没有改变的情况，不可能发生。

#### MGET命令 - 获取多个键值
执行MGET命令，将返回所有(一个或多个)给定 key 的值
语法格式：
```
MGET key [key ...]
```
示例 - 获取name、age、number  、sex的值。
```
127.0.0.1:6379> MGET name age number  sex
1) "jaak"
2) "24"
3) "158234232"
4) (nil)
```
如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。因此，该命令永不失败。

#### SETRANGE命令 - 覆写
SETRANGE命令是用 value 参数覆写(overwrite)给定 key 所储存的字符串值，从偏移量offset开始。
不存在的 key 当作空白字符串处理,语法格式：
```
SETRANGE key offset value
```
示例 - 覆写name的值。

```
127.0.0.1:6379> SET name "jaak"
OK
127.0.0.1:6379> SETRANGE name 4 "zhang"
(integer) 11
127.0.0.1:6379> GET name
"jaakzhang"
```
SETRANGE命令会确保字符串足够长以便将 value 设置在指定的偏移量上，如果给定 key 原来储存的字符串长度比偏移量小(比如字符串只有 5 个字符长，但你设置的 offset 是 10 )，那么原字符和偏移量之间的空白将用零字节(zerobytes, "\x00" )来填充。
注意你能使用的最大偏移量是 2^29-1(536870911) ，因为 Redis 字符串的大小被限制在 512 兆(megabytes)以内。如果你需要使用比这更大的空间，你可以使用多个 key 。

#### STRLEN命令 - 获取键值长度
STRLEN命令将会返回 key 所储存的字符串值的长度
语法格式：
```
STRLEN key
```
示例 - 获取name值的长度。
```
127.0.0.1:6379> STRLEN name
(integer) 9
```
值得注意的是当 key 储存的不是字符串值时，返回一个错误。

#### 其他命令
除了前面提到的那些常用命令以外，还有很多其他的命令，真正用到的时候再来详细了解如何使用它。

**命令**              　　　　　　　　　**用途**
**GETSET**  *设置键的字符串值，并返回旧值。*
**GETRANGE**    *得到字符串的子字符串存放在一个键。*
**GETBIT**  *对 key 所储存的字符串值，获取指定偏移量上的位(bit)。*
**SETBIT**  *对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。*
**SETNX**   *将 key 的值设为 value ，当且仅当 key 不存在。*
**MSETNX**  *同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。*
**PSETEX**  *和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。*
**INCR**    *将 key 中储存的数字值增一。*
**INCRBY**  *将 key 所储存的值加上指定增量。*
**INCRBYFLOAT** *为 key 中所储存的值加上指定浮点数增量。*
**DECR**    *将 key 中储存的数字值减一。*
**DECRBY**  *将 key 所储存的值加上指定增量。*

### 哈希（Hash）操作命令
Redis Hashes是字符串字段和字符串值之间的映射,因此他们是展现对象的完美数据类型。
#### HSET命令 - 添加键值
　　在 redis 中，使用HSET命令来将哈希表 key 中的域 field 的值设为 value 
语法如下：
```
HSET key field value
```
示例 - 添加键name ，值为‘jaak zhang’。
```
127.0.0.1:6379> HSET man name "jaak zhang" # 设置一个新域
(integer) 1
```
如果 key 不存在，一个新的哈希表被创建并进行HSET操作。
如果域 field 已经存在于哈希表中，旧值将被覆盖。

#### HMSET命令 - 添加多个值
除了HSET命令，HMSET命令的用途也是用来设置值，不同的是，HMSET一次可以设置多个 field-value (域-值)对设置到哈希表 key 中,语法如下：
```
HMSET key field value [field value ...]
```
示例 - 添加键age、sex。
```
127.0.0.1:6379> HMSET man age "24" sex "男"
OK
```
如果 key 不存在，将会创建一个空的哈希表并执行HMSET操作。
如果添加的域已存在哈希表中，那么它将被覆盖。

#### HGET命令 - 获取值
127.0.0.1:6379 中如何获取key的值呢？HGET是用来获取指定 key 值的命令，语法如下:
```
HGET key field
```
示例 - 获取域name的值。
```
127.0.0.1:6379> HGET man name
"jaak zhang"
```
执行HGET命令，如果 key 存在，将返回哈希表 key 中给定域 field 的值，如果 key 不存在，则返回 (nil) 。

#### HMGET命令 - 获取多个值
作为HMSET命令对应的获取命令，HMGET可以一次性获取哈希表 key 中，一个或多个给定域的值
基本语法：
```
HMGET key field [field ...]
```
示例 - 获取域name、age、sex的值。
```
127.0.0.1:6379> HMGET man  name age sex             
# 返回值的顺序和传入参数的顺序一样
1) "jaak zhang"
2) "24"
3) "\xe7\x94\xb7"
```
如果给定的域不存在于哈希表，那么返回一个 nil 值。
因为不存在的 key 被当作一个空哈希表来处理，所以对一个不存在的 key 进行HMGET操作将返回一个只带有 nil 值的表。

#### HGETALL命令 - 获取全部值
如果我们想要一次性获取全部域的值，很显然 HGET 和 HMGET 都是不合适的，所幸的是我们还有HGETALL命令，通过它，我们可以轻松的获取到全部域值，
基本语法：
```
HGETALL key
```
示例 - 获取man全部域的值。
```
127.0.0.1:6379>HGETALL man
1) "name"   #域
2) "jaak zhang" #值
3) "age"
4) "24"
5) "sex"
6) "\xe7\x94\xb7"
```
在返回值里，紧跟每个域名(field name)之后是域的值(value)，所以返回值的长度是哈希表大小的两倍。

#### HEXISTS命令 -  验证存在
在应用环境中，我们经常会需要知道一个 key 中是否存在某个 field ，HEXISTS命令可以帮助我们达到这个目的
基本语法：
```
HEXISTS key field
```
示例 - 验证键number是否存在。
```
127.0.0.1:6379> HEXISTS man number 
(integer) 0
```
查看哈希表 key 中，给定域 field 是否存在。
如果哈希表含有给定域，返回 1 。
如果哈希表不含有给定域，或 key 不存在，返回 0 。

#### HKEYS命令 - 获取所有key
我们经常会遇见这样的应用场景，比如在线用户列表、课堂列表等等，这时候我们可以使用HKEYS来获取哈希表 key 中的所有域
基本语法：
```
HKEYS key
```
示例 - 查看键man中所有的域。
```
127.0.0.1:6379> HKEYS man
1) "name"
2) "age"
3) "sex"
```
当 key 存在时，将返回一个包含哈希表中所有域的表。 当 key 不存在时，返回一个空表。

#### HLEN命令 - 获取域数量
HLEN命令将返回哈希表 key 中域的数量,什么时候会用到它呢？比如：在线聊天室，显示在线用户数
基本语法：
```
HLEN key
```
示例 - 查看man键中域的个数。
```
127.0.0.1:6379> HLEN man
(integer) 3
```
当 key 存在时，将返回哈希表中域的数量。 当 key 不存在时，返回 0 。

#### HDEL命令 - 删除
有添加就必定有删除的需求，当我们想要删除哈希表 key 中的一个或多个指定域时，可以使用HDEL命令
基本语法：
```
HDEL key field [field ...]
```
示例 - 删除键man中的sex域。
```
127.0.0.1:6379> HDEL man sex
(integer) 1
```
如果是不存在的域，那么它将被忽略掉。


