### 简介

RabbitMq是实现AMQP(高级消息队列协议)的消息中间件的一种，AMQP(Advanced Message Queuing Protocol),高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件协议.
RabbitMq在发送者/队列/接收者中间多了一层抽象.在发送者和队列之间加了交换器(Exchange),交换器根据调度策略把消息给队列

#### 组件

- Server/Broker: 接收客户端的连接,实现AMQP实体服务
- Connection: 连接,应用程序与Broker的连接
- **Channel**: 网络信道,所有操作都在Channel中进行,包括定义Exchange/定义Queue/绑定Exchange和Queue/发布消息等.客户端可以建立多个Channel,每个Channel代表一个会话任务.
- Message: 消息,服务器和应用程序之间传递的数据,由properties和body组成,properties对消息进行修饰,比如消息优先级,延迟等高级特性.body就是消息体内容.
- Virtual Host: 虚拟主,用户进行逻辑分离,最上层的消息路由.:一个虚拟主机包含一组交换机,队列和绑定.默认虚拟主机 "/"
- **交换机(Exchange)**:用于转发消息.根据**路由键**的规则决定发到哪个队列.以下四种类型:
  - Direct(默认,直连交换机): direct 类型的行为是”先匹配, 再投送”. 即在绑定时设定一个 **routing_key**, 消息的**routing_key** 匹配时, 才会被交换器投送到绑定的队列中去.
  - Topic(主题交换机):按规则转发消息(最灵活). *匹配一个单词, #匹配一个或多个单词, `.`是一个分隔符.
  - Headers() : 设置 header attribute 参数类型的交换机
  - Fanout(扇形交换机): 转发消息到所有的绑定队列中,没有任何判断,适合广播类的操作.
- **Queue,队列**:消息队列,保存消息并将他们转发给消费者,多个消费者可以同时订阅一个queue,queue会均分给消费者,不是每个消费者收到相同的消息.
- **Binding,绑定**: **交换机和队列之间的绑定**,binding中可以包含route key


#### 特点

1. RabbitMQ底层使用Erlang语言编写，传递效率高，延迟低
2. 开源、性能优秀、稳定性较高
3. 与SpringAMQP完美的整合、API丰富
4. 集群模式丰富、表达式配置、HA模式、镜像队列模式
5. 保证数据不丢失的情况下，做到高可用
6. AMQP全称：Advanced Message Queuing Protocol(高级消息队列协议)

#### MQ消息队列典型应用场景

- 异步处理
- 应用解耦
- 流量削峰
- 消息通讯

### 死信队列

#### 形成原因

1. 消息被拒绝，使用channel.basicNack或channel.basicReject,并且requeue属性被设置为false
2. 消息在队列的存活时间超过设置的TTL时间 
3. 消息队列的消息数量已经超过最大队列长度

死信消息会被mq进行特殊处理，如果配置了死信队列和死信交换器，会被丢进死信交换机，如果没有配置，消息将丢失。

#### 如何配置死信队列

1. 配置业务队列，绑定到业务交换器上
2. 为业务队列配置死信交换器和routing key
3. 为死信交换器配置死信队列

需要为每个使用死信的队列配置死信交换器，同一个项目的死信交换器可以共用一个，然后为每个业务队列分配一个单独的路由key
**业务队列如果配置了x-dead-letter-routing-key 参数，死信被投放到死信交换机时，会被替换为x-dead-letter-routing-key 参数的值，没有配置的话，保持原来的值**

### 常见的消息队列消息模式

1. Simple Work Queue (简单工作队列)： 常说的点对点模式，一条消息由一个消费者进行消费。当有多个消费者时，默认使用论寻机制把消息分配给消费者

2. Work Queues (工作队列)： 也叫公平队列，能者多劳的消息队列模型。队列必须接受来自消费者的手动ack才可以继续往消费者发送消息

3. Publish/Subscribe(发布订阅模式) ： 一条消息被多个消费者消费

4. Routing(路由模式) : 有选择的接收消息

5. Topics(主题模式)：通过一定的规则来选择性的接收消息

6. RPC模式： 发布者发布消息，并且通过RPC方式等待结果

7. Publisher Confimer(发布者确认模式)： 解决发布者与mq之间的消息可靠性


### RabbitMq怎么保证消息可靠

1. 生产者将消息发送到Exchange时丢失。例如发送过程中因为网络原因发送失败，或者发送到一个不存在的Exchange中 

  - 事物机制实现，对性能有很大影响，一般不采用

  - 通过生产者确认模式(Publish Confirm)实现

  > 事物和生产者确认模式是冲突的，只能启用一种。开启confirm模式之后，每次发送消息时都会生成一个唯一的ID，如果消息投递成功，mq会给生产者一个ack回执，通过ack可以知道是哪个消息发送成功


2. 路由失败。消息已经发送到Exchange中，但是Exchange根据Routing Key发送到Queue时失败。例如Exchange根本没有绑定Queue。

> RabbitMq在发送消息时提供了mandatory参数。如果mandatory为true时，Exchange根据自身的类型和Routing Key无法找到对应的Queue，不会丢掉该消息，会将消息返回给生产者。

3. 消费者在处理消息时失败。消费者已经接收到消息，但是在消息处理中出现异常，没有对异常处理，导致消息丢失。

> 默认情况下，消费者从队列中获取消息之后会自动ack，我们可以通过手动ack来保证消费者主动的控制ack行为，可以避免业务异常导致消息丢失。

4. RabbitMq宕机的情况下，没有做持久化处理，重启之后，未持久化的消息会丢失。

> 不仅需要持久化消息，还需要持久化Exchange和Queue，可以通过durable的值设为true来保证持久化。


### 常用操作

```shell
# 新增用户
rabbitmqctl add_user admin 123456
# 删除用户
rabbitmqctl delete_user admin
# 修改用户密码
rabbitmqctl  change_password  [Username]  [Newpassword]
#  查看当前用户列表 
rabbitmqctl list_users
# 设置用户角色
rabbitmqctl set_user_tags [username] [tags]
# 设置用户权限
rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
# 查看(指定hostpath)所有用户的权限信息 
rabbitmqctl list_permissions [-p VHostPath] 
# 查看指定用户的权限信息 
rabbitmqctl list_user_permissions User 
# 清除用户的权限信息 
rabbitmqctl clear_permissions  [-p VHostPath]  User
```
