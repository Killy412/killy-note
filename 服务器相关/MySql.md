# MySql

- 空间类型函数

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