# MongoDB

```shell
# windows启动mongoDB服务  切换到目录下
./mongod.exe --dbpath D:/mongodb/db
# windows下连接远程mongodb , 切换到目录下
.\mongo [ip]:[端口]/[数据库] -u [用户名] -p [密码]
# 默认连接语法
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
# 创建数据库
use [databaseName]
db   # 查看当前数据库
show dbs   # 查看所有数据库
db.dropDatabase()  # 删除数据库
db.createCollection([col],[options 键值对])     # 创建集合，类似数据库中的表
db.[col].drop()   # 删除集合(表)
db.[col].insert(docu)  # 插入文档 insert || save

$type操作符
db.[col].find().limit()  # 指定数量
db.[col].find().skip(1)  # 跳过的记录数
db.[col].find().sort({<key>:1})   # 排序  1:升序  -1:降序
db.[col].createIndex(keys,options)  # 创建索引
```
| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| :----------- | :--------------- | :---------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |


#### update方法
```shell
# 更新语法
db.[collectionName].update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

- **query** : update的查询条件，类似sql update查询内where后面的。
- **update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- **upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- **multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- **writeConcern** :可选，抛出异常的级别。

#### 删除

```
db.[collectionName].remove(
	<query>,
	{
		justOne:<boolean>,
		writeConcern:<docu>
	}
)
```

- **query** :（可选）删除的文档的条件。
- **justOne** : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
- **writeConcern** :（可选）抛出异常的级别。
#### 新增
```
db.COLLECTION_NAME.insert(document)
或
db.COLLECTION_NAME.save(document)
```
#### 查询

```shell
db.[collectionName].find(<query>,projection)
db.[co..].find().pretty()    # pretty以格式化方式显示所有文档
db.[].findOne()              # 只查询一个
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
)                            # or条件
```

| 操作       | 格式       | 范例                                      | RDBMS中的类似语句     |
| :--------- | :--------- | :---------------------------------------- | :-------------------- |
| 等于       | {:}        | db.col.find({"by":"菜鸟教程"}).pretty()   | where by = '菜鸟教程' |
| 小于       | {:{$lt:}}  | db.col.find({"likes":{$lt:50}}).pretty()  | where likes < 50      |
| 小于或等于 | {:{$lte:}} | db.col.find({"likes":{$lte:50}}).pretty() | where likes <= 50     |
| 大于       | {:{$gt:}}  | db.col.find({"likes":{$gt:50}}).pretty()  | where likes > 50      |
| 大于或等于 | {:{$gte:}} | db.col.find({"likes":{$gte:50}}).pretty() | where likes >= 50     |
| 不等于     | {:{$ne:}}  | db.col.find({"likes":{$ne:50}}).pretty()  | where likes != 50     |

#### 创建索引参数

| Parameter          | Type          | Description                                                  |
| :----------------- | :------------ | :----------------------------------------------------------- |
| background         | Boolean       | 建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为**false**。 |
| unique             | Boolean       | 建立的索引是否唯一。指定为true创建唯一索引。默认值为**false**. |
| name               | string        | 索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。 |
| dropDups           | Boolean       | **3.0+版本已废弃。**在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 **false**. |
| sparse             | Boolean       | 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 **false**. |
| expireAfterSeconds | integer       | 指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。 |
| v                  | index version | 索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。 |
| weights            | document      | 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。 |
| default_language   | string        | 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语 |
| language_override  | string        | 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language. |

#### 聚合

```shell
db.[col].aggregate(option)
```

