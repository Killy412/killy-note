# Redis相关
## 常用命令
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

##### 扩容

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


## redis geo地理位置相关指令(实现附近的人)

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