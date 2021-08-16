###1、介绍
redis中的数据类型，这里做一些基础总结。按照数据存储形式。底层原理，应用场景的基本路线实现。

 **5 种基本数据结构**

- `string ` : 字符串
- `hash`：主要用于存储对象，值就是key=value形式
- `list`：列表
- `set`：集合类型 string无序列表，实现方式是按照哈希，去重复
- `zset`：有序集合，去重复set

**其他常见数据类型**
- `geospatial ` ：地理位置
- `hyperloglogs `: 基数统计
- `Bitmap `:  位图
### 2、string字符串类型
存储数据结构:
![String](https://upload-images.jianshu.io/upload_images/10817794-4b49aafa03b7f3ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明：字符串可以存储任意类型的字符串
 **2.1、常用方法**
|  方法名称   |           使用方式           |        说明         |
| :---------: | :--------------------------: | :-----------------: |
|     set     |        set key value         |      添加键值       |
|     get     |           get key            |      获取键值       |
|    incr     |           incr key           |       自增加1       |
|   incrby    |        incr key long         | 增加自动long的长度  |
| incrbyfloat |    incrbyfloat key float     | 增加自动float的长度 |
|    decr     |           decr key           |        递减1        |
|   decrby    |       decrby key long        |  减少指定long长度   |
|    mget     |        mget key1 key2        |   同时获取多个key   |
|    mset     | mget key1 value1 key2 value2 |   同时设置多个key   |
|   strlen    |          strlen key          |     键值的长度      |
|   append    |       append key value       |   值尾部追加value   |
|   setbit    |      setbit key offset       |    二级制位运算     |
`注`: decr 没有decrbyfloat方法。
【示例】：
```
[127.0.0.1:6379> set stringkey 1234kfdf
OK
[127.0.0.1:6379> get stringkey
"1234kfdf"
```
**2.2、底层实现**
未完待续
**2.3、常见应用场景**
-  通过`incr` 做文章访问量统计
- 
未完待续
### 3、哈希(hash)散列类型
存储数据结构: vaule = {{key1,value1},{key2,value2}...{keyn,valuen}}
![image.png](https://upload-images.jianshu.io/upload_images/10817794-c8097360408af145.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
通过客户端工具（RDM）查看数据结构如下：

![image.png](https://upload-images.jianshu.io/upload_images/10817794-877542a302996c74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
说明：
redis是采用字典结构以键值对的形式存储(键 -- 键值)。键值是存的字段和字段值，其中，字段值只能是字符串类型不支持其他类型，即:散列类型不能嵌套其他类型。
**3.1、常用方法**
| 方法名称 |                  使用方式                   |                      说明                      |
| :------: | :-----------------------------------------: | :--------------------------------------------: |
|   hset   |        hset key  hashkey  hashvalue         |              增加key下的haskey值               |
|   hget   |              hget key  hashkey              |              查询key下hashkey的值              |
|  hmset   | hmset key  hashkey1  value1 hashkey2 value2 |              增加key下的haskey值               |
|  hmget   |        hmget key  hashkey1，hashkey2        |           批量查询key下的hashkey的值           |
| hgetall  |                 hgetall key                 | 查询key下所有hashkey及hashvalue值列表非map结构 |

**3.2、底层实现**
- 内部编码

**3.3、用用场景 **

### 4、list列表类型
redis中list列表结构是双向链表结构(Linked List),常用操作是在链表两端添加元素。本身是存储的一个有序的字符串。结果如下：
![image.png](https://upload-images.jianshu.io/upload_images/10817794-25f12ca4138643c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

生活例子：
苹果新品发布会，大家排队，新来的人直接向后排，这是宣布从队伍中选中排888号的果粉，可直接赠送，此时要从第1个向后数到888号。

**4.1、常用方法**
| 方法名称  |               使用方式                |                             说明                             |
| :-------: | :-----------------------------------: | :----------------------------------------------------------: |
|   lpush   |           lpush key  value            |  从列表左边插入元素，元素可以多个，返回插入数据后的列表长度  |
|   rpush   |           rpush key  value            |                从列表右边插入元素，元素可多个                |
|   lpop    |               lpop key                |          从列表左边剔除一个元素并向客户端返回该元素          |
|   rpop    |               rpop key                |          从列表右边剔除一个元素并向客户端返回该元素          |
|   llen    |               llen key                |                     返回列表中的元素个数                     |
|  lrange   |         lrang key  start  end         |      返回列表片段，这里"l"表示"list"列表意思，不是“左”       |
|   lrem    |        lrem key  count  value         | 列表中删count个值为value的元素，若count为正数，从左开始，若count负数，从右，若为0,全部删除 |
|  lindex   |           lindex key index            |          把列表当数组看，获取列表中指定index的元素           |
|   lset    |         lset key index value          |                设置index指定位置的元素值value                |
|   ltrim   |         ltrim  key start end          |       删除指定索引范围以外的元素，保留索引范围内的元素       |
|  linsert  | linsert  key before/after pivot value |     在指定pivot的后面插入元素value,插入方式看第二个参数      |
| rpoplpush |     rpoplpush source destination      | 从source列表右边剔除一个元素，并不当前元素从左边插入到destination列表中，最后返回这个元素 |

客户端工具RDM中查看List 类型如下：

![image.png](https://upload-images.jianshu.io/upload_images/10817794-5bdddce12928a719.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

【示例】：
```
127.0.0.1:6379> LPUSH books english chinese garmin
(integer) 3
127.0.0.1:6379> llen books
(integer) 3
127.0.0.1:6379> lrange books 0 10
1) "garmin"
2) "chinese"
3) "english"
127.0.0.1:6379> 
````
### 5、set集合类型

![image.png](https://upload-images.jianshu.io/upload_images/10817794-1e8ea883b44f6564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
集合和列表相比，属于无序元素唯一的数据结构。


**5.1、常用方法**
|  方法名称   |              使用方式               |                           说明                           |
| :---------: | :---------------------------------: | :------------------------------------------------------: |
|    sadd     |          sadd key  values           | 向集合key中添加一个或者多个元素,如果元素已经存在则会忽略 |
|    srem     |          srem key  values           |  从集合中删除一个或者多个元素，如果没匹配到元素则不删除  |
|  smembers   |            smembers key             |                   返回集合中所有的元素                   |
|  sismember  |        sismember key member         |               判断某个元素是否在集合中存在               |
|    sdiff    |          sdiff  key1  key2          |    求差集，返回只存在集合key1中元素(不存在key2集合中)    |
| sdiffstore  | sdiffstore  destination key1  key2  |     求key1,key2差集,并把结果存入destination目标集合      |
|   sinter    |         sinter  key1  key2          |     求交集，返回同时存在集合key1和key2集合中的元素集     |
| sinterstore | sinterstore  destination key1  key2 |     求key1,key2交集,并把结果存入destination目标集合      |
|   sunion    |         sunion  key1  key2          |      求并集，返回集合key1和key2中所有不重复的元素集      |
| sunionstore | sunionstore  destination key1  key2 |     求key1,key2并集,并把结果存入destination目标集合      |
|    scard    |             scard  key              |                      查看集合的大小                      |
| srandmember |       srandmember  key  count       |  随机返回集合中的count个元素,count为负数可返回重复数据   |
|    spop     |              spop  key              |                 随机从集合中剔除一个元素                 |

【样例】：
```
[127.0.0.1:6379> sadd scollection lights cup tree desk
```
![image.png](https://upload-images.jianshu.io/upload_images/10817794-55911246ef38bc1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6、zset有序集合

在集合基础上为每个元素增加了score分数，通过score分数高低对元素进行排序，某些方面和列表类似。

![image.png](https://upload-images.jianshu.io/upload_images/10817794-db783f7581318b5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**6.1、常用方法**
|     方法名称     |                 使用方式                 |                            说明                            |
| :--------------: | :--------------------------------------: | :--------------------------------------------------------: |
|       zadd       |          zadd key  score member          | 新增有序集合中的元素,key不存在则新增，可以多个score member |
|      zrange      |    zrange key  start stop withscores     |    获取有序集合中按分数从小到大，索引start stop中的集合    |
|    zrevrange     |   zrevrange key  start stop withscores   |                获取有序集合中按分数从大到小                |
|  zrangebyscore   |  zrangebyscore key  min max withscores   |                按照元素分数从小到大获取元素                |
| zrevrangebyscore | zrevrangebyscore key  max min withscores |                按照元素分数从大到小获取元素                |
|     zincrby      |      zincrby key  increment member       |           对有序集合元素member增加increment分数            |
|      zcard       |                zcard key                 |                    获得集合中的元素数量                    |
|      zcount      |            zcount key min max            |                  获得分数范围内元素的个数                  |
|       zrem       |             zrem key members             |                    删除一个或者多个元素                    |
| zremrangebyrank  |      zremrangebyrank key start stop      |                    按照排名范围删除元素                    |
|      zrank       |             zrank key member             |             获取元素排名, 按照分数从低到高获取             |
|     zrevrank     |           zrevrank key member            |             获取元素排名, 按照分数从高到低获取             |

【样例】
```bash
[127.0.0.1:6379[2]> zadd zsetLog 20 sdd
(integer) 1
[127.0.0.1:6379[2]> zadd zsetLog 10 java
(integer) 1
[127.0.0.1:6379[1]> zrangebyscore ranking 60 80 withscores limit 0 2
1) "\"jone\""
2) "60"
3) "\"sam\""
4) "60"
```
![image.png](https://upload-images.jianshu.io/upload_images/10817794-32d26c3fc6ed9fe4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**6.2底层实现**

散列 + 跳跃表（skip list）