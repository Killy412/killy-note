## 简介

### 基本使用过程

1. 创建`SqlSessionFactory`
2. 创建`SqlSession`
3. 使用`SqlSession`执行`sql`
4. 提交/回滚事务
5. 关闭会话

#### 一级缓存和二级缓存有什么区别

- 一级缓存是session级别，存储在`SqlSession`中,只要执行update操作时或者关闭session时就会清空缓存.默认打开
- 二级缓存是application级别,是按照namespace来存储的,执行update时会清空缓存.
