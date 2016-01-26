title: redis命令学习(三)
date: 2015-11-28 16:25:12
tags: redis
original: false
---
#### 有序集合(ZSet)操作命令
　　Redis 有序集合与普通集合非常相似，是一个没有重复元素的字符串集合。不同之处是有序集合的每个成员都关联了一个评分，这个评分被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但评分可以重复。
<!-- more -->
　　使用有序集合你可以以非常快的速度添加、删除和更新元素。因为元素是有序的, 所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。
　　访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。在有序集合中，你可以很快捷的访问一切你需要的东西：有序的元素，快速的存在性测试，快速访问集合的中间元素！ 简而言之使用有序集合你可以做完成许多对性能有极端要求的任务，而那些任务使用其他类型的数据库真的是很难完成的。

#### ZADD 命令 - 添加成员
在redis中，使用ZADD命令将一个或多个 member 元素及其 score 值加入到有序集 key 当中
基本语法：
```
ZADD key score member [[score member] [score member] ...]
```
示例 - 添加google.com,baidu.com,sougou.com到website集合，评分5 , 4 , 3。
```
127.0.0.1:6379> ZADD website 5 google.com 4 baidu.com 3 sougou.com                      
(integer) 3
```
如果某个 member 已经是有序集的成员，那么更新这个 member 的 score 值，并通过重新插入这个 member 元素，来保证该 member 在正确的位置上。
score 值可以是整数值或双精度浮点数。
如果 key 不存在，则创建一个空的有序集并执行ZADD操作。
当 key 存在但不是有序集类型时，返回一个错误。


#### ZRANGE命令 - 获取指定区域成员
如果想要获取集合成员，可以使用ZRANGE命令
基本语法：
```
ZRANGE key start stop [WITHSCORES]
```
示例 - 获取website集合的全部成员。                                                                                                                        
```
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES  # 显示整个有序集成员                                      
1) "sougou.com"                                                                
2) "3"                                                           
3) "baidu.com"                                                                 
4) "4"                  
5) "google.com"
6) "5"        
```
返回的成员的位置按 score 值递增(从小到大)来排序。

#### ZSCORE命令 - 获取成员评分
redis中使用ZSCORE命令来获取成员评分
基本语法：
```
ZSCORE key member
```
示例 - 获取集合website中元素google.com的评分。
```
127.0.0.1:6379> ZSCORE website google.com  # 注意返回值是字符串
"5"
```
如果 google.com 是集合 website 的成员，则返回成员 google.com 的评分值。
如果 google.com 元素不是有序集 website 的成员，或 website 不存在，则返回 nil 。

#### ZREM命令 - 移除成员
ZREM命令可以移除指定成员
基本语法：
```
ZREM key member [member ...]
```
示例 - 移除website集合中google.com元素。                                                                             
```
127.0.0.1:6379> ZREM website google.com    
(integer) 1
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES   #显示没有google.com 
1) "sougou.com"   
2) "3" 
3) "baidu.com"                      
4) "4"
```
执行成功，google.com元素将从website集合中移除，如果google.com不存在，将被忽略。
如果 website 集合存在但不是有序集类型时，返回一个错误。

#### ZCARD命令 - 获取成员数量
如果需要查看集合成员的数量，那么我们需要使用到ZCARD命令
基本语法：
```
ZCARD key
```
示例 - 查看website集合的成员数量。
```
127.0.0.1:6379> ZCARD website
(integer) 2
```
执行成功，将返回有序集 website 的成员总数。

#### ZCOUNT命令 - 获取评分区域成员数量
除了ZCARD命令以外，ZCOUNT命令也可以查看成员的数量，和前者不同的是，ZCOUNT命令可以设定评分的最小和最大值
基本语法：
```
ZCOUNT key min max
```
示例 - 查看评分在4-5之间的website。
```
127.0.0.1:6379> ZCOUNT salary 4 5  
(integer) 1
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES   # baidu.com 4        
1) "sougou.com" 
2) "3" 
3) "baidu.com"   
4) "4"
```
执行成功，将返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量。

#### ZRANK命令 - 排名
ZRANK命令可以获取到给定元素在集合中的排名，排名依据 评分（score） 值递增(从小到大)顺序排列
基本语法：
```
ZRANK key member
```
示例 - 显示 baidu.com 的评分排名。

```
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES                          
1) "sougou.com"                                                    
2) "3" 
3) "baidu.com"                                                   
4) "4"
127.0.0.1:6379> ZRANK website baidu.com 
(integer) 1
```
排名以 0 为底，也就是说， score 值最小的成员排名为 0 。
使用 ZREVRANK 命令可以获得成员按 score 值递减(从大到小)排列的排名。
```
127.0.0.1:6379> ZREVRANK website baidu.com     
(integer) 0                                
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES        
1) "sougou.com"                                            
2) "3"                                   
3) "baidu.com"                  
4) "4"  
```

#### ZINCRBY 命令 - 增量
ZINCRBY命令可以为给定的成员评分值加上增量
语法格式：
**ZINCRBY key increment member**
示例 - 为website集合中的baidu.com加分5。
```
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES                                  
1) "sougou.com"                                            
2) "3"                                                           
3) "baidu.com"                                                         
4) "4"  # 原来 4
127.0.0.1:6379> ZINCRBY website 5 baidu.com   # 加分5                       
"9"
127.0.0.1:6379> ZRANGE website 0 -1 WITHSCORES      
1) "sougou.com"                                                
2) "3"                                                          
3) "baidu.com"                                                    
4) "9"   # 加后 9
```
可以通过传递一个负数值 increment ，让 score 减去相应的值，比如 ZINCRBY key -5 member ，就是让 member 的 score 值减去 5 。
当 key 不存在，或 member 不是 key 的成员时， ZINCRBY key increment member 等同于 ZADD key increment member 。
当 key 不是有序集类型时，返回一个错误。
score 值可以是整数值或双精度浮点数。

### Key相关命令
#### KEYS命令 - 查找键
Redis 的keys命令用于管理键。使用 Redis 的keys命令,查找所有符合给定模式 pattern 的 key 。
语法格式：
```
KEYS pattern
```
示例 - 查找包含a的键。
```
127.0.0.1:6379> MSET name "zhang" age 24 sex 1   #设置三个键值对                                             
OK                                                                             
127.0.0.1:6379> KEYS *a*                                                       
1) "age"                                                              
2) "name"  
```
KEYS * 匹配数据库中所有 key 。
KEYS h?llo 匹配 hello ， hallo 和 hxllo 等。
KEYS h*llo 匹配 hllo 和 heeeeello 等。
KEYS h[ae]llo 匹配 hello 和 hallo 。
特殊符号用 \ 隔开。

#### EXISTS命令 - 判断key是否存在
EXISTS命令的作用是判断指定key是否存在
语法格式：
```
EXISTS key
```
示例 - 判断age是否存在。
```
127.0.0.1:6379> MSET name "zhang" age 24 sex 1                         
OK                                         
127.0.0.1:6379> EXISTS age                                                    
(integer) 1 
```
若 key 存在，返回 1 ，否则返回 0 

#### MOVE命令 - 移动key
MOVE命令的作用是将当前数据库的 key 移动到给定的数据库 db 当中
语法如下：
```
MOVE key db
```
如果当前数据库(源数据库)和给定数据库(目标数据库)有相同名字的给定 key ，或者 key 不存在于当前数据库，那么 MOVE 没有任何效果。
因此，也可以利用这一特性，将 MOVE 当作锁(locking)原语(primitive)。
示例 - 将数据库0中的age，移动到数据库1。
```
127.0.0.1:6379> SELECT 0   
# redis默认使用数据库0，为了清晰起见，这里再显式指定一次                       
OK                                       
127.0.0.1:6379> MOVE age 1                               
(integer) 1  
127.0.0.1:6379> EXISTS age                                                 
(integer) 0                                                                    
127.0.0.1:6379> SELECT 1                                                  
OK                                                                             
127.0.0.1:6379[1]> EXISTS age   
#注意命令提示符变成127.0.0.1:6379[1] 表明正在使用数据库 1                                                                   
(integer) 1 
```
#### RENAME命令 - 重命名
RENAME命令可以将原有的 key 修改为新的key名称
语法如下：
```
RENAME key newkey
```
示例 - 重命名name 为 lastname。
```
127.0.0.1:6379> GET  name     # 获取当前name的值  
"zhang"                                                                        
127.0.0.1:6379> RENAME name lastname # 重命名name 为 lastname            
OK                                                                             
127.0.0.1:6379> GET name   # 获取当前name的值 已经不存在                       
(nil)
127.0.0.1:6379> GET lastname                                                   
"zhang"
```
当 key 和 newkey 相同，或者 key 不存在时，返回一个错误。
当 newkey 已经存在时， RENAME 命令将覆盖旧值。
#### SORT命令 - 排序
排序是很常见的需求，在 redis 中可以使用SORT命令来实现排序
语法格式：
```
SORT key [BY pattern] [LIMIT offset count] [GET pattern [GET pattern ...]] [ASC | DESC] [ALPHA] [STORE destination]
```
示例 对一个number列表排序
```
127.0.0.1:6379> LPUSH number 6 2 5 9 1 3 8                               
(integer) 7                                                                    
127.0.0.1:6379> SORT number                                                    
1) "1"                                                                         
2) "2"                                                                         
3) "3"                                         
4) "5"    
5) "6"     
6) "8" 
7) "9"
127.0.0.1:6379> SORT number DESC   
1) "9"            
2) "8"    
3) "6"   
4) "5"
5) "3"
6) "2"
7) "1" 
```
使用SORT命令，可以返回或保存给定列表、集合、有序集合 key 中经过排序的元素。
排序默认以数字作为对象，值被解释为双精度浮点数，然后进行比较。

#### DUMP命令 - 序列化
redis 支持序列化，使用DUMP命令来序列化给定key的值
语法如下：
```
DUMP key
```
示例 - 序列化name。
```
127.0.0.1:6379> SET name "jaak"
OK
127.0.0.1:6379> GET name
"jaak"
127.0.0.1:6379> DUMP name
"\x00\x04jaak\x06\x00\x02\xd6\xed3n\x91d\xe5"
```
执行DUMP命令序列化成功后，将返回被序列化的值，若key不存在，则返回 nil 。

#### EXPIRE命令 - 设置生存时间
为key设置生存时间需要使用EXPIRE命令
语法格式：
```
EXPIRE key seconds
```
示例 - 设置name的过期时间为20秒。
```
127.0.0.1:6379> GET name
"jaak" 
127.0.0.1:6379> EXPIRE name 20
(integer) 1
 # 20秒后执行
127.0.0.1:6379> GET name
(nil) 
```
为给定 key 设置生存时间，当 key 过期时(生存时间为 0 )，它会被自动删除。
在 Redis 中，带有生存时间的 key 被称为『易失的』(volatile)。

生存时间可以通过使用 DEL 命令来删除整个 key 来移除，或者被 SET 和 GETSET 命令覆写(overwrite)，这意味着，如果一个命令只是修改(alter)一个带生存时间的 key 的值而不是用一个新的 key 值来代替(replace)它的话，那么生存时间不会被改变。

#### TTL命令 - 获取剩余生存时间
TTL命令的作用是获取给定 key 剩余生存时间(TTL, time to live)
语法格式：
```
TTL  key
```
示例 - 查看key剩余生存时间。
```
127.0.0.1:6379> EXPIRE sex 1000
(integer) 1
127.0.0.1:6379> TTL key
(integer) 996
```
当 key 不存在时，返回 -2 。 当 key 存在但没有设置剩余生存时间时，返回 -1 。 否则，以秒为单位，返回 key 的剩余生存时间

