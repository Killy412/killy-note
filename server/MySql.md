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