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

  

### 多库配置
```java
//默认0库
@Bean(name = "redisTemplate")//负责存储token
public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {//工厂默认创建
    RedisTemplate<String, String> redisTemplate = new RedisTemplate<String, String>();
    redisTemplate.setConnectionFactory(redisConnectionFactory);
    return redisTemplate;
}
 
@Bean(name = "jedisTemplate")//做查询缓存
public RedisTemplate<String, Object> jedisTemplate() {
    RedisStandaloneConfiguration config = new RedisStandaloneConfiguration();
    //默认0号库，现在这里是1号库
    config.setDatabase(1);
    config.setPassword(password);//本地暂时没有密码
    LettuceConnectionFactory lettuceConnectionFactory = new LettuceConnectionFactory(config);//手动创建工厂，这样做的目的就是划分库
 
    lettuceConnectionFactory.setValidateConnection(true);
    lettuceConnectionFactory.afterPropertiesSet();
    RedisTemplate<String, Object> redisTemplate = new RedisTemplate<String, Object>();
    redisTemplate.setConnectionFactory(lettuceConnectionFactory);
    redisTemplate.setKeySerializer(new StringRedisSerializer());
    redisTemplate.setValueSerializer(new JdkSerializationRedisSerializer());
    return redisTemplate;
}
```

### 自定义序列化方式
```java
private RedisTemplate<String, Object> createRedisTemplate() {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        //设置序列化方式
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        // 开启事务
        redisTemplate.setEnableTransactionSupport(true);
        return redisTemplate;
}
```

### 创建连接工厂

```java
    private LettuceConnectionFactory createConnectionFactory(int database) {
        RedisStandaloneConfiguration config = new RedisStandaloneConfiguration();
        config.setDatabase(database);
        config.setPassword(GlobalConfig.config.getPassword());
        config.setHostName(GlobalConfig.config.getHost());
        config.setPort(GlobalConfig.config.getPort());

        //手动创建工厂，这样做的目的就是划分库
        LettuceConnectionFactory lettuceConnectionFactory = new LettuceConnectionFactory(config);

        lettuceConnectionFactory.setValidateConnection(true);
        lettuceConnectionFactory.afterPropertiesSet();
        return lettuceConnectionFactory;
    }
```

### 要在启动类去掉redis自动配置,否则上线会有问题

```java
@SpringBootApplication(exclude = RedisAutoConfiguration.class)
```

