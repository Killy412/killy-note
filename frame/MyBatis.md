## 简介

### 基本使用过程

1. 创建`SqlSessionFactory`
2. 创建`SqlSession`
3. 使用`SqlSession`执行`sql`
4. 提交/回滚事务
5. 关闭会话

### 工作原理

1. 读取Mybatis配置文件-mybatis-config.xml,加载映射文件，文件中配置了操作数据库的sql语句，最后生成一个配置对象。
2. 构造会话工厂，通过mybatis的环境等配置信息构建会话工厂`SqlSessionFactory`
3. 创建会话对象：由会话工厂创建`SqlSession`对象，该对象中包含了执行Sql语句的所有方法
4. `Executor`执行器：Mybatis底层定义了一个Executor接口来操作数据库，它将根据SqlSession传递的参数动态的生成需要执行的sql，同时负责查询缓存的维护
5. StatementHandler：数据库会话器，串联起参数映射的处理和运行结果映射的处理。
6. 参数处理：对输入参数的类型进行处理，并预编译
7. 结果处理：对返回结果的类型进行处理，根据对象映射规则，返回相应对象

### MyBatis功能架构

- API接口层：提供给外部使用的API，开发人员通过这些本地API来操纵数据库，接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理
- 数据处理层：负责具体的sql查找/sql解析/sql执行/执行结果映射处理等。主要目的是根据调用的请求完成一次数据库操作
- 基础支撑层：负责最基础的功能支撑，包含连接管理/事务管理/配置加载/缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件，为上层的数据处理层提供最基础的支撑

### 为什么Mapper接口不需要实现类

使用动态代理

1. SqlSession通过`getMapper`方法，首先获取MapperProxyFactory(mapper代理工厂)
2. MapperProxyFactory的作用是生成MapperProxy(Mapper代理对象)
3. 在MapperProxy里，通常会生成一个MapperMethod对象，是通过cachedMapperMethod方法对其进行初始化，然后执行execute方法
4. mapperProxy的`execute()`方法，会执行真正的sql，用到了**命令设计模式**

### Mybatis都有哪些Executor执行器

主要有三种执行器

- `SimpleExecutor`，每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象
- `ReuseExecutor`，执行update或select，以sql作为key查找statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String,Statement>对象中，下次使用，重复使用Statement对象
- `BatchExecutor`，执行update，将所有sql都添加到批处理中(addBatch())，等待统一执行(executeBatch()),它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch批处理，与jdbc批处理相同。

#### Mybatis如何指定使用哪一种Executor执行器

- 在MyBatis配置文件中，在设置(settings)可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数
- 配置默认的执行器。Simple就是普通的执行器；Reuse执行器会重用预处理语句；Batch执行器将重用语句并执行批量操作；

### 一级缓存和二级缓存有什么区别

- 一级缓存是session级别，存储在`SqlSession`中,只要执行update操作时或者关闭session时就会清空缓存.默认打开
- 二级缓存是application级别,是按照namespace来存储的,执行update时会清空缓存.

