title: redis命令学习(二)
date: 2015-11-27 15:45:51
tags: redis
original: false
---
#### 列表(Lists)操作命令
　　Redis列表是简单的字符串列表，按照插入顺序排序。
<!-- more -->
你可以添加一个元素导列表的头部（左边）或者尾部（右边）LPUSH命令插入一个新的元素导头部,而RPUSH插入一个新元素导尾部.当一个这两个操作在一个空的Key上被执行的时候一个新的列表被创建。相似的，如果一个列表操作清空一个列表那么对应的key将被从key空间删除。这是非常方便的语义，因为他们被调用使用一个空列表完全就像他们被调用时使用一个不存在的键值（可以）做为参数

#### LPUSH命令 - 添加元素
LPUSH的作用是将一个或多个值 value 插入到列表 key 的**表头**
基本语法：
```
LPUSH key value [value ...]
```
示例：将java添加到语言列表。
```
127.0.0.1:6379> LPUSH languages "java"
(integer) 1
```
如果有多个 value 值，那么各个 value 值按从左到右的顺序依次插入到表头,比如说，对空列表 mylist 执行命令 LPUSH mylist a b c ，列表的值将是 c b a 。
如果 key 不存在，一个空列表会被创建并执行LPUSH操作。
执行成功时，返回列表长度，当 key 存在但不是列表类型时，返回一个错误。

#### LSET命令 - 设置指定位置元素
LSET可以将列表 key 下标为index的元素的值设置为 value 
基本语法：
```
LSET key index value
```
示例
```
127.0.0.1:6379> LSET languages 0 "nodejs"
OK
```
需要注意的是，列表 key 必须是已存在的，而且index不能超出列表长度范围。

#### LINDEX命令 - 获取指定位置元素 
如果要获取列表元素，LINDEX命令是比较常用的，使用LINDEX，我们可以获取到指定位置的 value 
基本语法：
```
LINDEX key index
```
示例 - 获取languages的第一个元素。
```
127.0.0.1:6379> LINDEX languages 0
"nodejs"
```
下标 (index)为正数时，0表示第一个元素，1表示第二个元素，以此类推。
下标 可以是负数，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。

#### LPOP命令 - 取出元素
LPOP命令执行时会移除列表第一个元素，并将其返回
基本语法：
```
LPOP key
```
示例 - 取出languages中的第一个元素。
```
127.0.0.1:6379> LPOP languages
"nodejs"
```
请注意，LPOP命令会移除列表中的元素，如果仅仅是想要获取该元素，那么就不应该使用LPOP操作，因为redis中有专门获取元素的命令。

#### LINSERT命令 - 插入元素
插入元素是一个必要功能，LINSERT可以将值 value 插入到列表 key 当中，位于值 pivot 之前或之后
基本语法：
LINSERT key BEFORE|AFTER pivot value
示例 - 将node插入到JS之前。
```
127.0.0.1:6379> LPUSH languages "java" "JS"
(integer) 2
127.0.0.1:6379> LINSERT languages BEFORE "JS" "node"
(integer) 3
```
当 pivot 不存在于列表 key 时，不执行任何操作。
当 key 不存在时， key 被视为空列表，不执行任何操作。
如果 key 不是列表类型，返回一个错误。

#### LREM命令 - 移除元素
在redis中，移除列表元素使用LREM命令，根据参数 count 的值，移除列表中与参数 value 相等的元素
基本语法：
```
LREM key count value
```
示例 - 移除languages中，所有的名叫‘JS’的元素。
```
127.0.0.1:6379>  LREM languages 0 "JS"
(integer) 1
```
count 的值可以是以下几种：
count > 0 : 从表头开始向表尾搜索，移除与 value 相等的元素，数量为 count 。
count < 0 : 从表尾开始向表头搜索，移除与 value 相等的元素，数量为 count 的绝对值。
count = 0 : 移除表中所有与 value 相等的值。

#### LEN命令 - 列表长度

在redis中，LLEN命令可以获取到列表的长度，基本语法：
```
LLEN key
```
示例 - 查看languages列表长度。
```
127.0.0.1:6379> LLEN languages
(integer) 2
```
返回列表 key 的长度。
如果 key 不存在，则 key 被解释为一个空列表，返回 0 。
如果 key 不是列表类型，返回一个错误。

#### LTRIM命令 - 剪切列表
LTRIM可以对一个列表进行修剪，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除
基本语法：
```
LTRIM key start stop
```
示例 - 只保留列表 languages 的前2个元素，其余元素全部删除。
```
127.0.0.1:6379> LTRIM languages 0 1
OK
```
下标(index)参数start和stop都以 0 为底，也就是说，以 0 表示列表的第一个元素，以 1 表示列表的第二个元素，以此类推。
你也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。

### 集合(Set)操作命令
Redis 集合（Set）是一个无序的字符串集合. 。Redis 集合拥有令人满意的不允许包含相同成员的属性。多次添加相同的元素，最终在集合里只会有一个元素。 实际上说这些就是意味着在添加元素的时候无须检测元素是否存在。 一个Redis集合的非常有趣的事情是他支持一些服务端的命令从现有的集合出发去进行集合运算，因此你可以在非常短的时间内进行合并（unions）, 求交集（intersections）,求差集（differences of sets）。
#### SADD命令 - 添加
集合操作中，SADD命令可以将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略
基本语法：
```
SADD key member [member ...]
```
示例 - 添加‘Tom’到room集合中。
```
127.0.0.1:6379> SADD room "Tom"
(integer) 1
```
假如 key 不存在，则创建一个只包含 member 元素作成员的集合。
当 key 不是集合类型时，返回一个错误。

#### SPOP命令 - 随机取出
如果我们需要随机取出集合中的某个元素，可以使用SPOP命令
基本语法：
```
SPOP key
```
示例:随机取出room集合中的元素。
```
127.0.0.1:6379> SPOP room
"Tom"
```
需要注意的是，执行SPOP命令返回的元素将被**移除**该集合。

#### SMEMBERS命令 - 获取全部元素
如果要获取集合中全部的元素，则需要使用SMEMBERS命令
基本语法：
```
SMEMBERS key
```
示例 - 获取room集合中全部的元素。
```
127.0.0.1:6379> SMEMBERS room
(empty list or set)
```
SMEMBERS命令只会返回集合中的全部成员，并不会移除它们，如果集合不存在，则视为空集合。

#### SCARD命令 - 元素数量
如果想要查看集合中元素的数量，可以使用SCARD命令
基本语法：
```
SCARD key
```
示例 - 查看room集合中元素的数量。
```
127.0.0.1:6379> SCARD room
(integer) 0
```
执行SCARD命令，当集合存在时，返回集合中元素的数量，若集合不存在，则返回0。

#### SDIFF命令 - 差集
假如现在有两个集合，我们想要获取到它们之间不同的元素，通常情况下，我们需要通过循环集合来比较，然后取得不同的元素。
在redis里面取得集合的差集非常简单，通过SDIFF命令即可轻松实现
基本语法：
```
SDIFF key [key ...]
```
示例 - 取得room1和room2的差集。
```
127.0.0.1:6379> SADD room1 "Tom" "Jack" 
(integer) 2
127.0.0.1:6379> SADD room2 "jaakzhang" "Tom"
(integer) 2
127.0.0.1:6379> SDIFF room1 room2 
#这里room1在前 返回的结果以room1为主体 从room1中去掉room2中相同的元素
1) "Jack"
127.0.0.1:6379> SDIFF room2 room1 
#这里room2在前 返回的结果以room2为主体 从room2中去掉room1中相同的元素
1) "jaakzhang" 
```
如果 key 都存在，则返回一个集合的全部成员，该集合是所有给定集合之间的差集。
不存在的 key 被视为空集。

#### SINTER命令 - 交集
在 redis 中获取集合的交集也是非常简单的，执行SINTER命令将返回集合的交集
基本语法：
```
SINTER key [key ...]
```
示例 - 获取集合room1和room2的交集。
```
127.0.0.1:6379>  SMEMBERS room1
1) "Jack"
2) "Tom"
127.0.0.1:6379>  SMEMBERS room2
1) "jaakzhang"
2) "Tom"
127.0.0.1:6379> SINTER room1 room2
1) "Tom"
127.0.0.1:6379> SINTER room2 room1
1) "Tom"
```
当集合都存在时，将返回一个集合的全部成员，该集合是所有给定集合的交集。
不存在的集合被视为空集。因此，当给定集合当中有一个空集时，结果也为空集(根据集合运算定律)。

#### SUNION命令 - 并集
既然有差集和交集运算，当然少不了并集，在 redis 中，执行SUNION命令将返回给定集合的并集
基本语法：
```
SUNION key [key ...]
```
示例：获取集合room1和room2的并集。
```
127.0.0.1:6379>  SMEMBERS room1
1) "Jack"
2) "Tom"
127.0.0.1:6379>  SMEMBERS room2
1) "jaakzhang"
2) "Tom"
127.0.0.1:6379> SUNION room1 room2
1) "jaakzhang"
2) "Jack"
3) "Tom"
```
如果给定的集合都存在，则返回一个集合的全部成员，该集合是所有给定集合的并集。
同样，不存在的集合被视为空集。

#### SISMEMBER命令 - 包含判断
如果要判断集合是否包含某个元素也不需要循环对比了，因为 redis 提供SISMEMBER命令可以实现这个功能
基本语法：
```
SISMEMBER key member
```
示例 - 判断 Tom 元素是否集合 room1 的成员。
```
127.0.0.1:6379> SMEMBERS room1
1) "Jack"
2) "Tom"
127.0.0.1:6379>  SISMEMBER room1 "Tom"
(integer) 1
```
如果集合包含给定的元素，则返回1，反之则返回0。

#### SMOVE命令 - 移动元素
执行SMOVE可以移动元素
基本语法：
```
SMOVE source destination member
```
将 member 元素从 source 集合移动到 destination 集合。SMOVE是原子性操作，因此可以保证数据的一致性。

示例 - 将room1集合中的‘Jack’移动到‘room2’集合。
```
127.0.0.1:6379>  SMEMBERS room1
1) "Jack"
2) "Tom"
127.0.0.1:6379>  SMEMBERS room2
1) "jaakzhang"
2) "Tom"
127.0.0.1:6379> SMOVE room1 room2 "Jack"
(integer) 1
127.0.0.1:6379>  SMEMBERS room2
1) "Jack"
2) "jaakzhang"
3) "Tom"
127.0.0.1:6379>  SMEMBERS room1
1) "Tom"
```
如果 source 集合不存在或不包含指定的 member 元素，则SMOVE命令不执行任何操作，仅返回 0 。否则， member 元素从 source 集合中被移除，并添加到 destination 集合中去。
当 destination 集合已经包含 member 元素时，SMOVE命令只是简单地将 source 集合中的 member 元素删除。
当 source 或 destination 不是集合类型时，返回一个错误。

#### SREM命令 - 移除元素
执行命令SREM可以将元素从集合中移除
基本语法：
```
SREM key member [member ...]
```
示例 - 从room1集合中移除"Tom"

```
127.0.0.1:6379> SMEMBERS room1
1) "Tom"
127.0.0.1:6379> SREM room1 "Tom"
(integer) 1
127.0.0.1:6379> SMEMBERS room1
(empty list or set)
```
移除集合 key 中的一个或多个 member 元素，不存在的 member 元素会被忽略。
当 key 不是集合类型，返回一个错误。