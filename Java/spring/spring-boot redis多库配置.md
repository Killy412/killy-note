# redis多库配置

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

