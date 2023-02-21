# MySql

## MqSql架构

1. 连接层 管理客户端和服务连接，权限认证
2. 服务层: 一些sql接口，解析器，优化器，缓存
3. 存储引擎: 负责MySql中数据的存储和提取，服务层通过API与存储引擎进行通信
4. 数据存储层： 将数据存储在运行于裸设备的文件系统之上，并完成和存储引擎的交互

### MySql执行流程

1. 客户端通过协议将SQL语句发送给MySQL服务器
2. 先查询缓存，如果缓存命中，直接返回缓存数据。未命中，进入下一个环节
3. 服务层进行SQL解析，预处理，然后查询优化器生成对应的执行计划
4. 服务器根据查询优化器给出的执行计划，再调用存储引擎的API执行查询
5. 将结果返回给客户端，如果开启了缓存，备份一份到查询缓存中

#### sql语句的执行顺序

> FROM: 选取表,将多个表数据通过笛卡尔积生成一个虚拟表
> ON: 对笛卡尔积生成的虚表进行筛选
> WHERE: 对虚表进行筛选
> GROUP BY: 分组
> HAVING: 对分组进行筛选
> SELECT: 返回的数据列表
> DISTINCT: 去重
> ORDER BY: 排序
> LIMIT: 行数限制

## 事物相关

### 事务日志

1. redo log(重做日志) 
2. undo log(回滚日志)
3. binlog(二进制日志)
4. error log(错误日志)
5. slow query log(慢查询日志)
6. general log(一般查询日志)
7. relay log(中继日志)

![事务执行时，各种log记录的流程](../img/redo_log_process.png)

#### redo log(重做日志): 保证事物ACID中的持久性
  InnoDB作为存储引擎,数据都是存放在磁盘中,如果每次读取数据都读取磁盘,速度会很慢. InnoDB提供了缓存(Buffer Pool) 加快读取速度.Buffer Pool中保存对磁盘中部分数据页的映射.解决CPU和磁盘之间的读写速度差异。
  有两部分组成，redo log buffer(保存在内存中，容易丢失)和redo log file（保存在硬盘中，保证持久化）。

  redo log流程

  1. 先将原始数据读取到内存中，修改数据的内存拷贝
  2. 生成一条重做日志(redo log)并写入到redo log buffer，记录的是被修改后的值
  3. 当事务commit的时候，将redo log buffer中的内容刷新到redo log file中，对redo log file采用追加写的方式
  4. 定期将内存中修改的数据刷新到磁盘中

  刷盘策略
  redo log buffer刷盘到redo log file中，并不是真正的刷到磁盘中，只是刷盘到**文件系统缓存 Page Cache中**，现代操作系统为了提高写磁盘效率做的优化。真正的写入会交给系统来决定。交给系统也存在系统宕机，数据丢失的情况。InnoDB给出`innodb_flush_log_at_trx_commit`参数，该参数控制刷盘的频率。
  - 该值为0：每次事务提交时不进行刷盘操作.(系统master thread 默认每隔一秒进行重做日志)
  - 该值为1：每次事务提交时都进行刷盘操作
  - 该值为2：表示每次事务提交时都只是把redo log buffer内容写入到Page Cache中，不进行同步，由系统决定什么时候进行同步。

  redo log采用的是WAL(write ahead logging) 预写式日志,所有修改先写入日志,再更新到buffer pool.

redo log 也需要在事务提交时将日志写入磁盘，为什么它比直接将 Buffer Pool 中修改的数据写入磁盘(即刷脏)要快呢?

主要有以下两方面的原因：

- 刷脏是随机 IO，因为每次修改的数据位置随机，但写 redo log 是追加操作，属于顺序 IO。
- 刷脏是以数据页(Page)为单位的，MySQL 默认页大小是 16KB，一个 Page 上一个小修改都要整页写入;而 redo log 中只包含真正需要写入的部分，无效 IO 大大减少。

#### undo log(回滚日志): 实现事务的原子性和隔离性(MVCC)。提供回滚操作。

  > 当事物对数据库做了修改,InnoDB会生成对应的undo log;
  >
  > 事物执行失败或者调用了rollback,innodb会根据undo log做与之前相反的操作 : 对于insert 回滚时执行delete;对于delete,回滚是执行insert;对于update,回滚时执行相反的update;
  >
  > 当事物执行update时,在生成的undo log中包含被修改的主键,修改的列,列的修改前后值等信息.
  
  MVCC多版本并发控制通过undo log实现。客户端读取一行记录时，若该记录已经被其他事务占用，可以通过读取undo log之前的行版本信息，实现非锁定读取。
  
  **存储机制**：undo log由InnoDB存储引擎实现。**采用分段(segment)存储。rollback segment称为回滚段，每个回滚段中包含1024个undo log segment**。目前InnoDB支持128个回滚段，支持同时开启的事务数量是128×1024个。
  undo log存放数据更新前的记录/rowId/事务Id/回滚指针。回滚指针指向的是上一次的undo log日志，会形成一条undo log回滚链。回滚链中的第一条undo log的回滚指针是NULL。

  **工作原理**: 事务开始的时候，针对每条修改sql生成对应的undo log。事物提交时放入一个删除列表中，等待purge线程来删除
  
  

#### binlog 二进制日志文件

记录了数据库所有执行的DDL和DML语句(除了select和show等查询语句)，以事件形式记录并保存在二进制文件中。
两种应用场景，用于复制，master通过binlog传递给slaves达成主从数据一致的目的。用于数据恢复，例如还原备份后，可以重新执行备份后新产生的binlog，使数据库保持最新的状态。

mysql binlog有三种工作模式
1. Row level
日志中会记录每一行数据被修改的情况，然后在slaves端对相同的数据进行修改
能清楚的记录每一行数据修改的细节
缺点是数据量太大
2. statement level
每一条被修改数据的sql都会被记录到master的binlog中，slaves在复制的时候会解析成和master一样的sql语句执行，会导致主从双方数据不一致，比如uuid的产生
3. mixed
结合row level和statement level 的优点，但是数据量也大

#### binlog和redo log区别

作用不同:

- redo log 是用于 crash recovery 的，保证 MySQL 宕机也不会影响持久性;
- binlog 是用于 point-in-time recovery 的，保证服务器可以基于时间点恢复数据，此外 binlog 还用于主从复制。

层次不同：

- redo log 是 InnoDB 存储引擎实现的，
- 而 binlog 是 MySQL 的服务器层实现的，同时支持 InnoDB 和其他存储引擎。

内容不同：

- redo log 是物理日志，内容基于磁盘的 Page。
- binlog 是逻辑日志，内容是一条条 sql。

写入时机不同：

- redo log 的写入时机相对多元。前面曾提到，当事务提交时会调用 fsync 对 redo log 进行刷盘;这是默认情况下的策略，修改 innodb_flush_log_at_trx_commit 参数可以改变该策略，但事务的持久性将无法保证。

除了事务提交时，还有其他刷盘时机：如 master thread 每秒刷盘一次 redo log 等，这样的好处是不一定要等到 commit 时刷盘，commit 速度大大加快。

- binlog 在事务提交时写入。

### 并发事物产生的问题

- 脏读: 一个事物读取了另一个事物未提交的事物.
- 丢失修改: 一个事物的修改被另一事物的修改覆盖了.
- 不可重复读: 一个事物在两次读取中间,另一事物修改了数据,产生同一事物两次读取情况不一样.
- 幻读: 一个事物读取了几行数据,然后另一事物并发插入几行数据,第一个事物再次查询,多出一些第一次没读到的数据,称为幻读.

### 隔离级别

- read uncommitted(RU):读未提交,产生脏读

  > RU是对数据记录加锁,不是对事物加锁

- read committed(RC): 不可重复读,一个事务中多次读取的数据可能不一致

  > RC每次执行sql都会生成一个read view,每次读取的是不同的.

- repeatable read(RR): 可重复读,解决了脏读/不可重复读的问题,一个事物中多次读取的数据是一致的. 

  > 使用MVCC(多版本并发控制协议)保证RR的隔离正确性,是不上锁的.
  >
  > 使用NextKey避免幻读的产生.
  >
  > InnoDB存储引擎关于间隙锁
  >
  > - Record Lock: 行锁,锁住记录行
  > - Gap Lock: 间隙锁,锁住一个区间内的数据行。传统的行锁只能锁定已经存在的数据行，无法阻止插入新行，使用Gap Lock解决幻读。
  > - NextKey Lock: RecordLock+GapLock, 锁定一个范围.依照左闭右开的原则。
  >
  > 当索引key包含唯一索引时(等值查询时),NextKey Lock会降为Gap Lock.

- serializable: 串行化

#### MVCC:多版本并发控制，主要为了提高并发的技术

> **InnoDB下的mvcc是通过数据行的两个隐藏列实现的. 分别保存行的创建时间和过期时间(删除时间). 存储的并不是实际的时间值,而是系统版本号. 每开始一个新的事物,系统版本号都会自动递增.事物开始时刻的系统版本号会作为事物的版本号,用来和查询到的每行记录的版本号做比较.在repeatable read隔离级别下,mvcc具体操作.**

- select: 只查找版本号小于当前事物版本号的数据行|未定义删除版本号或者删除版本大于当前版本号的数据行
- insert: 为插入的每一行保存当前系统版本号作为行版本号
- delete: 为删除的每一行保存当前系统版本号作为删除标识
- update: 为插入一行新记录,保存当前系统版本号为行版本号,同时保存当前系统版本号为原来行作为行删除标识

> MVCC是行级锁的变种,读的时候避免加锁操作.多个版本的数据共存,主要依赖数据的隐藏列和undo log实现.
>
> 隐藏列的数据包括版本号,删除时间,指向undo log的指针等.
>
> 通过next-keylock实现RR级别的避免幻读. next-keylock相当于实现record lock(记录锁)和gap lock(间隙锁),特点是不仅会锁住数据记录,也会锁住一个范围(gap lock的功能).


## 锁

- 相关名词
  - 表级锁:MyIsam是表级锁,更新一条记录要锁住整个表,性能较低,并发不高,不存在死锁问题
  - 页级锁
  - 行级锁:并不是直接锁记录,而是锁索引.如果一条sql操作了主键索引,会锁住这条主键索引
  - 共享锁(S锁 MyIsam是读锁):对某一资源加共享锁,都可以进行读,无法修改.必须所有共享锁释放之后,才可以修改
  ```mysql
  select * from table lock in share mode
  ```
  - 排他锁(X锁 MyIsam是写锁):对某一资源加排他锁,自己可以进行增删改查,其他无法进行任何操作
  
  ```mysql
  select * from table for update --增删改自动加了排他锁
  ```
  
  - 悲观锁(抽象):在操作数据时,认为此操作会出现数据冲突,所以要获取锁才能进行操作,悲观锁是数据库自己实现,共享锁和排他锁属于悲观锁的范畴
  - 乐观锁(抽象):用数据库version实现
  - 意向锁: 某行修改时,给记录加上排他锁,同时给表加意向锁,表示有记录正在锁定,这是,其他人就不能对表加表锁了
  
- 数据库增删改操作默认增加排他锁



## 索引

### 索引类型

1. 主键索引
2. 唯一索引
3. 普通索引
4. 全文索引
5. 空间索引
6. 外键索引

- B-Tree索引: **适合查找范围数据或者键前缀查询**
  - 全值匹配
  - 匹配最左前缀
  - 匹配列前缀
  - 匹配范围值
  
  B树索引在不同的存储引擎中实现方式不同:
  
  - MyIsam: **叶节点data区域存放的是数据行的地址..**在检索索引的时候,首先根据BTree搜索算法搜索索引,如果指定的key存在,则取出data域的值,然后以data域的值作为地址读取相应的数据记录.  **也被称为非聚簇索引.**
  - InnoDB:  **数据文件就是索引文件. 叶节点data区域存放的是数据行数据. 索引的key是数据表的主键,因此表数据文件就是主索引.也被称为聚簇索引.** 其余的索引作为辅助索引,辅助索引的data域存储的是对应主键的值. 使用主键索引时,直接根据key找到对应的数据行. 使用辅助索引时,需要先找到对应的主键key,再走一遍主键索引.这种叫做回表. 因此,不建议使用过长的字段作为主键.
  

### 聚簇索引和非聚簇索引的区别

聚簇索引将索引和数据放在一起，也称物理索引，MySql只能有一个聚簇索引，一般是主键索引。
非聚簇索引是索引和数据分开存储。查找指定值时比较快。

### 覆盖索引

因为二级索引中data域存储的是主键的值,正常查询使用二级索引的话,会再走一遍主键索引,这个步骤叫回表.而如果查询的列被包含在索引key中,就不需要走一遍回表步骤,这种情况称为覆盖索引.

### 索引下推

是MySQL5.6版本推出的.用于优化查询的. 在使用复合索引的时候,假如查询条件中的两个列都被包含在索引中,此时这个sql有两种查询方式,第一种根据索引找出符合第一列的主键,并回表把所有数据行查出来,再把符合第二列条件的筛选出来. 第二种是在使用二级索引时,查询符合第一列的条件时,同时判断第二列是否符合条件,如果不符合,直接筛掉,减少了回表的次数.第二种就是索引下推.

- hash索引: **只存储hash值和行指针,不存储值.不支持范围查找数据,无法排序. **

  - 支持等值比较查询. 例如 = ,in() ,<=> ,

  > 支持等值比较查询. 如果多个列的hash值相同, 会以链表的方式存储在一个hash条目中.



## Sql优化

### sql优化最主要的三点

1. 最大化利用索引
2. 尽可能避免全表扫描
3. 较少无效数据的查询

### sql优化的方法

- 避免字段开头模糊查询
- 避免使用数据类型的隐式转换
- 避免使用`select *`
- 避免使用子查询,可以把子查询优化为join查询
- 使用in 代替or .
- 禁止对列进行函数操作或者运算. 会导致无法走索引.

### 查询索引 explain关键词

- type列的值
  - all:即全表扫描，意味着MySQL需要从头到尾去查找所需要的行。这种情况下需要增加索引来进行优化.
  - index:扫描全表索引，通常比All快一些
  - **range** :范围扫描通常出现在in(), between,>,<,>=等操作中。使用一个索引来检索给定范围的行.
  - **ref** :相比eq_ref，不适用唯一索引，而是使用普通索引或者唯一索引的部分前缀，索引要和某个值相比较，可能会找到多个符合条件的行。
  - **eq_ref** :primay key或 unique key索引的所有部分被连接使用，最多只会返回一条符合条件的记录。这可能是const之外最好的联接类型，简单的select查询不会出现这种type。
  - **const** :mysql能对查询的某部分进行优化并将其转换成一个常量（可看成是show warnings的结果）。用于primay key或unique key的所有列与常数比较时，所以表最多有一个匹配行，读取1次，速读较快。
  - system 是const的特例，表中只有一行元素匹配时为system
- extra的值
  - using where: 会把数据行从存储引擎查询出来之后再进行过滤
  - using index: 使用覆盖索引
  - using index condition:  使用索引下推
  - using temporary: 对查询结果排序时会使用临时表.

## 存储引擎

- innodb: 常用的存储引擎,支持事物,支持行锁/表锁
- myisam: 不支持事物,查询新增数据比较快,只支持表锁


## 新增数据,已经存在就更新

```mysql
-- 已经存在数据就忽略
insert ignore into [table] ([col],[col]) values ([value],[value]);
-- 插入数据,存在就更新,不存在新增
insert into [table] ([col]...) values ([values]...) on duplicate key update [col]=[value];
-- replace可以实现和上面一样的效果,前提必须有唯一索引,具有原子性,返回1,说明没有重复操作,返回2说明先删除后新增
replace into [table] ([col]..) values([val]...)
```

## 死锁查看相关sql

```mysql
# 当前事物
select * from information_schema.INNODB_TRX;

# 获取死锁日志
show engine innodb status;

# 事物隔离级别
select @@tx_isolation;

# 查询是否锁表
show OPEN TABLES where In_use > 0;

# 查询进程
show processlist

# 在 MySQL 8.0 中，增加了一个新的动态变量：innodb_deadlock_detect，可以用于控制 InnoDB 是否执行死锁检测。该参数的默认值为 ON，即打开死锁检测。
show variables like 'innodb_deadlock_detect';
```




## 字段类型

### 数据类型

| 类型|大小  | 范围（有符号）| 范围（无符号） |    用途 |
| :----------: | :--------------------------------------: | :----------------------------------------------------------- | :----------------------------------------------------------: | --------------: |
|   TINYINT    |  1 byte | (-128，127) | (0，255) |小整数值 |
|   SMALLINT   | 2 bytes | (-32 768，32 767)  |  (0，65 535)  | 大整数值 |
|  MEDIUMINT   | 3 bytes| (-8 388 608，8 388 607)  | (0，16 777 215)|大整数值 |
| INT或INTEGER | 4 bytes | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295)|大整数值 |
|    BIGINT    |  8 bytes | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)| (0，18 446 744 073 709 551 615)|极大整数值 |
|    FLOAT |  4 bytes| (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)  | 单精度 浮点数值 |
|    DOUBLE    |  8 bytes| (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
|   DECIMAL  | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值 |依赖于M和D的值 |小数值 |


### 空间类型函数

```shell
# 字符串转为内部存储的二进制数据
[ST_]GeomFromText('POINT(123.462202 41.804471)');
# 列转为字符串
[ST_]AsText(列名)
# 计算两点的距离
[ST_]Distance_Sphere(POINT(121.590347, 31.388094),location)
```

- 使用存储过程造数据

```sql
drop procedure test_insert;
#创建存储过程
CREATE PROCEDURE test_insert()
    #开始
BEGIN
    #定义变量
    DECLARE i long DEFAULT 1225410207544710119;
    #条件判断
    WHILE i < 1225410207544820119
        #执行
        DO
        #SQL
#             INSERT into tbl_user(birthday,`open_id`,`phone`,`nick_name`,`sex`,`often_city`,`want_sex`,`avatar`,`want_rave_id`,`last_latitude`,`last_longitude`,`geo_hash`)
#             value('2019-10-18',i,'',concat('张三',i),0,'杭州',1,'123.jpg',1,34,117,'rrr');
            insert into tbl_match_record(uid, is_like, match_uid)
                value (i, 0, i + 10);
            #变量增加
            SET i = i + 1;
            #结束循环
        END WHILE;
    #提交
    commit;
    #结束
END;
#执行
CALL test_insert();
#删除存储过程
drop procedure test_insert;
#查看存储过程
SHOW PROCEDURE STATUS;

```