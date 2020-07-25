# 网关限流

### 常用限流算法

##### 计数器

##### 漏桶算法

##### 令牌桶算法

```
spring:
  cloud:
    gateway:
      routes:
        - id: limit_route
          uri: http://httpbin.org:80/get
          predicates:
          - Path=/api/**
          filters:
          - name: RequestRateLimiter
            args:
              key-resolver: '#{@hostAddrKeyResolver}'
              redis-rate-limiter.replenishRate: 1
              redis-rate-limiter.burstCapacity: 3
```

在上面的配置文件，配置了 redis的信息，并配置了RequestRateLimiter的限流过滤器，该过滤器需要配置三个参数：

- burstCapacity：令牌桶总容量。每秒最大处理的请求数量
- replenishRate：令牌桶每秒填充平均速率。每秒允许处理的请求数量
- key-resolver：用于限流的键的解析器的 Bean 对象的名字。它使用 SpEL 表达式根据#{@beanName}从 Spring 容器中获取 Bean 对象。

###### IP限流

获取请求用户ip作为限流key。

```java
@Bean
public KeyResolver hostAddrKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getRemoteAddress().getHostName());
}
```

###### 用户限流

获取请求用户id作为限流key。

```java
@Bean
public KeyResolver userKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getQueryParams().getFirst("userId"));
}
```

###### 接口限流

获取请求地址的uri作为限流key。

```java
@Bean
KeyResolver apiKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getPath().value());
}
```

