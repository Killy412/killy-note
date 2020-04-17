# springboot集成redis使用

- 启动类加上注解

```java
@EnableCache
```

- @Cacheable注解:对方法的返回结果进行缓存
  - cacheName/value:缓存名
  - key:键
  - cacheManager: 缓存管理器
  - sync:是否使用异步模式
  - condition: 符合条件则缓存,对参数进行判断(调用方法之前和之后都会判断)
  - unless: 符合条件则不缓存,对result进行判断(方法执行后判断)
- @CachePut: 执行之后存入缓存,一般用在新增的方法上
  - value/cacheName
  - key:键
  - cacheManager
- @CacheEvict: 清空指定的缓存,用在修改/删除的方法上
  - value/cacheName
  - key
  - cacheManager
  - condition: 条件符合则缓存
  - allEntries: 是否清空所有缓存,默认:false;
  - beforeInvocation: 方法执行之前清空缓存
- @Caching:同时应用多个缓存注解功能

- @CacheConfig:抽取缓存的公共配置(类级别)

  