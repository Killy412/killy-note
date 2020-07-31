# MySql

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

### 锁
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

- 乐观锁


### 索引

#### 查询索引 explain关键词
- type列的值
  - all:即全表扫描，意味着MySQL需要从头到尾去查找所需要的行。这种情况下需要增加索引来进行优化.
  - index:扫描全表索引，通常比All快一些
  - **range** :范围扫描通常出现在in(), between,>,<,>=等操作中。使用一个索引来检索给定范围的行.
  - **ref** :相比eq_ref，不适用唯一索引，而是使用普通索引或者唯一索引的部分前缀，索引要和某个值相比较，可能会找到多个符合条件的行。
  - **eq_ref** :primay key或 unique key索引的所有部分被连接使用，最多只会返回一条符合条件的记录。这可能是const之外最好的联接类型，简单的select查询不会出现这种type。
  - **const** :mysql能对查询的某部分进行优化并将其转换成一个常量（可看成是show warnings的结果）。用于primay key或unique key的所有列与常数比较时，所以表最多有一个匹配行，读取1次，速读较快。system 是const的特例，表中只有一行元素匹配时为system。



### 新增数据,已经存在就更新

```mysql
-- 已经存在数据就忽略
insert ignore into [table] ([col],[col]) values ([value],[value]);
-- 插入数据,存在就更新,不存在新增
insert into [table] ([col]...) values ([values]...) on duplicate key update [col]=[value];
-- replace可以实现和上面一样的效果,前提必须有唯一索引,具有原子性,返回1,说明没有重复操作,返回2说明先删除后新增
replace into [table] ([col]..) values([val]...)
```

### 死锁查看相关sql

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

