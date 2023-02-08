# Redis

## 命令

```shell
redis-cli                                    # 登录
auth ""                                      # 密码认证
set <key> <value>                            # 设置键值对
get <key>                                    # 获取
exists <key>                                 # 判断是否存在
ttl <key>                                    # 查看过期时间(秒)
pttl <key>                                   # 查看过期时间(毫秒)
expire <key>  <expire_time>                  # 设置过期时间
strlen <key>                                 # 对应值的长度
del <key>                                    # 删除对应的键
type <key>                                   # 查看数据结构类型
rename <key> <newkey>                        # 重命名 newkey不存在时才会重命名
randomkey                                    # 随机返回一个key

```

### redis实现主从相关命令

```shell
# 在从库执行
slaveof <master-ip> <master-port>  #<master-ip>为主库服务ip，<master-port>表示主库所在端口，默认6379
config set masterauth <master-pass>  # 访问主库的密码

info replication  # 查看主从信息
```

### redis.conf配置

```shell
bind 127.0.0.1  # 可访问ip
daemonize yes   # 让redis服务后台运行
requirepass slavepassword # 从库密码，可选配置
timeout 0    #当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能
loglevel     # 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice
save <seconds> <changes>  # 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
#Redis 默认配置文件中提供了三个条件：
#save 900 1
#save 300 10
#save 60 10000
#分别表示 900 秒(15分钟)内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改.
maxclient  # 最大客户端连接数,默认为redis可以同时打开的最大的文件描述符
# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
maxmemory <bytes>

# 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no
appendonly no
appendfilename appendonly.aof #指定更新日志文件名，默认为 appendonly.aof
# 指定更新日志条件，共有 2 个可选值：
#  no：表示等操作系统进行数据缓存同步到磁盘（快）
#  always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）
#  everysec：表示每秒同步一次（折中，默认值）
appendsync everysync 


#指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
vm-enabled no
# 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
vm-swap-file /tmp/redis.swap
#将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
vm-max-memory 0
# Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不确定，就使用默认值
vm-page-size 32
# 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
vm-pages 134217728

# <masterip>表示主库所在的ip，而<masterport>则表示主库启动的端口，默认是6379
slaveof <masterip> <masterport>
# 主库有密码必需要配置，<master-password>代表主库的访问密码
masterauth <master-password>

#作为从服务器，默认情况下是只读的（yes），可以修改成NO，用于写（不建议）。
slave-read-only yes

# 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
include /path/to/local.conf
```

## 常用数据类型

### string

字符串,可变的字节数组,最大长度为512M,小于1M,扩容是加倍现有空间,大于1M,每次扩容1M

```shell
set <key> <value>                            # 设置键值对
get <key>                                    # 获取
exists <key>                                 # 判断是否存在
ttl <key>                                    # 查看过期时间(秒)
pttl <key>                                   # 查看过期时间(毫秒)
expire <key>  <expire_time>                  # 设置过期时间
strlen <key>                                 # 对应值的长度
del <key>                                    # 删除对应的键
type <key>                                   # 查看数据结构类型
rename <key> <newkey>                        # 重命名 newkey不存在时才会重命名
getrange <key> <start> <end>                 # 获取子串,指定位置开始和结束
append <key>  <value>                        # 追加字串
incrby                                       # +
decrby                                       # -
incr                                         # +1
decr                                         # -1
```

### hash

hash,使用二维结构,第一维是数组,第二维是链表.hash内容的key和value放在链表中,数组保存的是链表的头指针.通过key查找元素时,先计算key的hashcode,再根据hashcode值对数组取模运算,定位到对应的链表,再对链表遍历进行取值.链表的作用就是用来将产生**hash碰撞**的元素串起来

#### 扩容

当hash比较拥挤时(hash碰撞比较频繁),就要进行扩容. 会申请两倍旧数组大小,然后将所有链表重新分配到新的数组中.

```shell
hset <key> <field> <value>                   # 设置值
hget <key> <field>                           # 获取值
hdel <key> <field>
hlen <key>                                   # field 个数
hmget <key> <field> [...]
hmset <key> <field> [...]
hkeys <key>                                  # 获取所有field
hvals <key>                                  # 获取所有value
hgetall <key>
hstrlen <key> <field>                        # 获取字段长度
hexists <k> <f>                              # 判断是否有某field
hincrbyfloat <k> <f> <increment>             # key指定字段增加数值(float)
hincrby <k> <f> <increment>                  # key指定字段增加数值(int)
hscan <k> <cursor>                           # 迭代hash表中的值
```

### list

列表,存储结构为链表.双向链表,首尾插入删除效率高.下表可以为负数.

```shell
# 新增
rpush <key> <value> [value...]                # 右边插入元素
lpush <key> <value> [value...]                # 左边插入元素
linsert <key> before/after <value> <value>    # 指定位置添加元素
# 查找
lrange <key> <start> <end>                    # 查看
lindex <key> <index>                          # 获取指定下标的元素
llen <key>                                    # 列表长度
# 删除
lpop <key>
rpop <key>
lrem <key> <count> <value>
# Lrem命令会从列表中找到=value的元素进行删除，根据count的不同分为3中情况
# Count>0,从左到有，删除最多count个元素
# Count<0,从右到左，删除最多count绝对值个元素
# Count=0,删除所有
# 修改
lset <key> <index> <value>
```

### set

集合:内部使用的hash结构,所有value指向同一个內部值

```shell
sadd <key> <e>                              # 添加值
srem <key> <e>                              # 删除
scard <key>                                 # 元素个数
sismember <k> <e>                           # 在集合中则返回1，否则返回0
srandmember <k> [count]                     # 随机返回指定个数,count可不写，默认为1
spop <k> [count]                            # 随机返回元素
```

### zset 
有序集合,在集合的基础上进行延伸有序,K是V的一个键值对,键值对的分数score:V,默认从小到大
```shell
zadd <key> <score> <member> [score,member...] # 向zset中添加值
zrange <key> 0 -1;                            # 查看值
zrange <key> 0 -1 withscores;                 # 查看值和分数

zrangebyscore <key> <startScore> <endScore> [withscores]   # 局部范围的分数

zrem <key> <member>                           # 删除元素

zcard <key>                                   # 获取zset值的数量
zcount <key> <startScore> <endScore>          # 获取指定分值区间的值数量
zrank <key> <member>                          # 获取该值的索引下标
zscore <key> <member>                         # 获取该值对应分数
```

### GEOHASH地理位置相关指令(实现附近的人)

Redis结合有序队列zset以及geohash编码,实现空间搜索功能.Geo模块六个指令

- GEOADD(geo add):将给定的位置对象(经度/维度/名字)添加到指定的key.
- GEOPOS(geo pos):从key里面返回所有给定位置对象的位置(经度/纬度)
- GEODIST(geo dist):返回两个给定位置之间的距离
- GEOHASH(geo hash):返回一个或多个给定位置的geohash表示
- GEORADIUS(geo radius):以给定的经纬度为中心,返回目标集合中与中心的距离不超过给定最大距离所有位置对象
- GEORADIUSBYMEMBER(geo radius by member):以给定的位置对象为中心,返回与其距离不超过给定的最大距离的所有位置对象

> 组合使用geoadd和georadius实现附近的人中增/查功能.实现类似微信附近的人功能,使用geo radius by member命令
> 
> 使用时注意点:
> 1. redis geo操作只包含增/查功能,没有删除功能.因为redis使用zset保存位置对象,使用zrem删除
> 

使用方式
> 范围单位(unit) m | km | ft | mi --> 米 | 千米 | 英尺 | 英里
```shell
geoadd key longitude latitude member [long...]      # 添加对象
georadius <key> <lon> <lat> <radius> <unit>         # 查找位置对象,参数
geopos <key> <member>                               # 查看对象的经纬度
geodist <key> <member1> <mbmber2> [unit]            # 查看两个对象距离
geohash <key> <member>                              # 查看对象hash值
georadiusbymember <key> <member> <radius> <unit>    # 查看对象指定距离的对象
```

> 经差一度距离: 91291 m
>    一分        : 1420  m
>    一秒        : 23.6 m 
> 纬差一度距离: 110950 m
>     一分   1850 m
>     一秒   30.9 m

#### [GeoHash](./GeoHash.md)


### 计算机硬件相关知识点

在进程看来,访问的是连续的内存空间. 由计算机的页表机制映射到物理内存上. 

线程上下文切换很慢.  **CPU执行是根据以时钟周期为单位(一个时钟周期为1纳秒(ns)). 寻址内存需要1~10us(微秒),磁盘寻址需要1~10ms(毫秒)**

进程中的线程映射在linux内存中是task

## Redis为什么这么快

> Redis单线程是指redis处理客户端连接,读/解析/执行/写 都是由一个线程完成的. 而其他的清理脏数据,无用连接释放,LRU淘汰策略是由其他线程完成的. 本质上也是多线程.

- 基于内存
- 基于事件驱动
- IO多路复用机制
  - 同时监控多个Socket
  - select/poll
    - 监控多个file descriptor
    - 当返回时,需要轮询FD列表检查是否有事件发生
  - epoll
    - 当返回时,只有有事件发生的FD会被返回
- 6.0之前单线程.  6.0之后引入worker Thread,只处理网络IO的读取和写入,核心IO负责串联处理客户端命令
- **Redis瓶颈主要是网络和内存,不存在CPU造成瓶颈的情况,所以是单线程的.**

### 多路IO复用模型

IO多路复用模型是利用select/poll/epoll可以同时监察多个流的io事件能力. 在空闲的时候,会把线程阻塞掉,当有一个流或多个流有io事件时, 把线程从阻塞状态唤醒, 去轮询流(epoll只轮询有io事件的流). 避免了大量的无用操作. 

**多路指的是多个网络连接,复用指的一个线程处理多个流** 

采用IO多路复用技术可以让单个线程高效的处理多个连接请求(减少线程上下文切换的开销时间),且redis在内存中操作数据的速度非常快,内存操作不能造成影响redis性能的因素.

## 跳跃表

以有序的方式在层次化的链表中保存元素,

## Redis 发布订阅模式

- 优点
  - 跨平台/跨语言的消息总线
  - 简单方便
- 缺点
  - 太简单
  - 不支持重复消费(订阅者不在线的时候无法收到消息)

## Redis过期和淘汰机制

redis配置文件中可以配置redis使用的内存最大值,如果达到最大值再写数据,redis有以下几种策略处理

> **LRU算法: less recently used 最少使用,是一种缓存置换算法. 核心思想是: 一个数据在最近没有被使用到,在将来也被使用的可能也很小,所以被淘汰掉.**
>
> **LFU算法: Least Frequently used, 核心思想是根据key的被访问频率进行淘汰,很少被访问的优先被淘汰. 被访问多的被留下. LFU算法只在4.0以后的版本有效. **
>
> LFU算法有两种淘汰策略
>
> 1. volitile-lfu: 在设置了过期时间的key中使用lfu算法进行淘汰 
> 2. allkeys-lfu: 在所有key中使用lfu算法进行淘汰
>
> **LFU算法能更好的表示数据的热度,**

- **noevication(默认策略): **对于写请求不再提供服务,直接返回错误.(DEL请求和部分特殊请求除外)
- **allkeys-lru:** 从所有key中使用lru算法进行淘汰
- **volatile-lru: **从设置了过期时间的key中使用lru算法进行淘汰
- **allkeys-random:** 从所有key中随机淘汰数据
- **volatile-random: ** 从设置了过期时间的key中随机淘汰
- **volatile-ttl:  ** 从设置了过期时间的key中根据过期时间进行淘汰, 越早过期优先被淘汰

> 当使用volatile-lru/volatile-random/volatile-ttl策略时,如果没有key可以被淘汰,依然返回错误.

1. 从设置过期的key中淘汰一批最早过期的key
2. 从设置过期的key中淘汰一批热度最小的key
3. 从所有key中淘汰一批最早过期的key
4. 从所有key中淘汰一批热度最小的key
5. 随机淘汰
6. 禁止驱除数据. 内存不足时,写入数据会报错.

## Redis过期数据的删除策略

- 惰性删除: 不会主动删除,查询key时先判断是否过期,如果过期则删除.
- 定期删除: 随机抽取100个key,检查过期时间.


## 缓存雪崩和缓存击穿

- 缓存击穿: **大量的请求同时查询一个key,此时key失效,会导致大量的请求达到数据库**.造成数据库某一时刻压力巨大.
- 缓存雪崩:  **某一时刻大规模缓存失效. 进来的请求都落在数据库上,造成服务宕机.**

### [缓存击穿以及布隆过滤器](./Redis%E7%BC%93%E5%AD%98%E5%87%BB%E7%A9%BF%E4%BB%A5%E5%8F%8A%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8.md)

## 持久化方式

- AOF(append-only file):  存储命令,增量. 优点: 灵活/快速. 缺点: 文件会越来越大.
  - 默认每秒sync一次
- RDB:全量数据, 数据快照存储方式
- Redis4.0 支持混合持久化,AOF重写的时候就是把RDB的内容直接写到AOF文件中,可读性不高.  好处是结合了AOF和RDB的优点.

使用RDB有两种命令,save/bgsave

- save: 阻塞用户线程,较快
- bgsave: 后台执行,不阻塞用户进行,较慢.

