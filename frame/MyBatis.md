## 简介



#### 一级缓存和二级缓存有什么区别

- 一级缓存是session级别,只要执行update操作时或者关闭session时就会清空缓存.
- 二级缓存是application级别,是按照namespace来存储的,执行update时会清空缓存.
