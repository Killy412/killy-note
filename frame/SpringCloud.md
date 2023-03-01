# Spring Cloud

## 简介

Spring Cloud是一个分布式的整体解决方案。Spring Cloud 为开发者提供了在分布式系统（配置管理，服务发现，熔断，路由，微代理，控制总线，一次性token，全局琐，leader选举，分布式session，集群状态）中快速构建的工具，使用Spring Cloud的开发者可以快速的启动服务或构建应用、同时能够快速和云平台资源进行对接。

SpringCloud分布式开发五大常用组件

- 服务发现/注册中心——Netflix Eureka/Consul/Zookeeper/Nacos
- 客户端负载均衡/服务调用——Netflix Ribbon/Feign
- 断路器——Netflix Hystrix/Turbine
- 服务网关——Netflix Zuul/Gateway
- 集群监控——Turbine/hystrix-dashboard
- 分布式配置——Spring Cloud Config/nacos
- 服务追踪——Spring Cloud Sleuth

### 网关Gateway

Gateway是在Spring生态系统之上构建的API网关服务，基于Spring 5，Spring Boot 2和 Project Reactor等技术。Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能， 例如：熔断、限流、重试等.

Spring Cloud Gateway有如下特性

- 基于Spring Framework 5, Project Reactor 和 Spring Boot 2.0 进行构建；
- 动态路由：能够匹配任何请求属性；
- 可以对路由指定 Predicate（断言）和 Filter（过滤器）；
- 集成Hystrix的断路器功能；
- 集成 Spring Cloud 服务发现功能；
- 易于编写的 Predicate（断言）和 Filter（过滤器）；
- 请求限流功能；
- 支持路径重写。

路由规则

- 通过时间匹配
- 通过cookie匹配
- 通过header属性匹配
- 通过host匹配
- 通过请求方法(put/post/get/delete)匹配
- 通过请求路径匹配
- 通过请求参数匹配
- 通过ip地址匹配

##### 过滤器
- GatewayFilter  作用在单个路由上
- GlobalFilter   作用在全局

#### 限流算法

- 计数器

- 漏桶算法

- 令牌桶算法

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

- IP限流

获取请求用户ip作为限流key。

```java
@Bean
public KeyResolver hostAddrKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getRemoteAddress().getHostName());
}
```

- 用户限流

获取请求用户id作为限流key。

```java
@Bean
public KeyResolver userKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getQueryParams().getFirst("userId"));
}
```

- 接口限流

获取请求地址的uri作为限流key。

```java
@Bean
KeyResolver apiKeyResolver() {
    return exchange -> Mono.just(exchange.getRequest().getPath().value());
}
```


### 负载均衡/服务调用

#### Feign声明式服务调用

#### restTemplate

#### 日志级别

- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。

#### ribbon服务调用

#### <font color="red">Ribbon和 Feign的区别</font>

- Ribbon添加maven依赖 spring-starter-ribbon  使用@RibbonClient(value="服务名称")  使用RestTemplate调用远程服务对应的方法。feign添加maven依赖 spring-starter-feign  服务提供方提供对外接口 调用方使用  在接口上使用  @FeignClient("指定服务名")
- Ribbon和Feign都是用于调用其他服务的，不过方式不同。
- 启动类使用的注解不同，Ribbon用的是@RibbonClient，Feign用的是@EnableFeignClients。
- 服务的指定位置不同，Ribbon是在@RibbonClient注解上声明，Feign则是在定义抽象方法的接口中使用@FeignClient声明。
- 调用方式不同，Ribbon需要自己构建http请求，模拟http请求然后使用RestTemplate发送给其他服务，步骤相当繁琐。
- Feign则是在Ribbon的基础上进行了一次改进，采用接口的方式，将需要调用的其他服务的方法定义成抽象方法即可，不需要自己构建http请求。不过要注意的是抽象方法的注解、方法签名要和提供服务的方法完全一致。

## 注解

- @EnableDiscoveryClient和@EnableEurekaClient都可以注册服务
