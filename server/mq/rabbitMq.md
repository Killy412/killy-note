# MQ概念

### MQ消息队列典型应用场景

- 异步处理
- 应用解耦
- 流量削峰
- 消息通讯

### 常见的消息队列消息模式
1. P2P模式
> 其中包含三个角色,消息队列,发送者,接收者.
> 特点:每个消息只有一个消费者（Consumer）(即一旦被消费，消息就不再在消息队列中)发送者和接收者之间在时间上没有依赖性，也就是说当发送者发送了消息之后，不管接收者有没有正在运行，它不会影响到消息被发送到队列,接收者在成功接收消息之后需向队列应答成功 如果希望发送的每个消息都会被成功处理的话，那么需要P2P模式。
2. pub/sub模式,发布/订阅
> 包含主题/发布者/订阅者
> 特点: 每个消息可以有多个消费者发布者和订阅者之间有时间上的依赖性。针对某个主题(topic)的订阅者，它必须创建一个订阅者之后，才能消费发布者的消息。为了消费消息，订阅者必须保持运行的状态。为了缓和这样严格的时间相关性，有些MQ消息队列（比如RabbitMQ）允许订阅者创建一个可持久化的订阅。这样，即使订阅者没有被激活（运行），它也能接收到发布者的消息。如果希望发送的消息可以不被做任何处理、或者只被一个消息者处理、或者可以被多个消费者处理的话，那么可以采用Pub/Sub模型。

3. push/pull模式

### rabbitMq是实现AMQP(高级消息队列协议)的消息中间件的一种
> AMQP(Advanced Message Queuing Protocol),高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件协议.
> rabbitMq在发送者/队列/接收者中间多了一层抽象.在发送者和队列之间加了交换器(Exchange),交换器根据调度策略把消息给队列
> 

### 特点：

1. RabbitMQ底层使用Erlang语言编写，传递效率高，延迟低
2. 开源、性能优秀、稳定性较高
3. 与SpringAMQP完美的整合、API丰富
4. 集群模式丰富、表达式配置、HA模式、镜像队列模式
5. 保证数据不丢失的情况下，做到高可用
6. AMQP全称：Advanced Message Queuing Protocol
7. AMQP翻译:高级消息队列协议

#### 四个概念
- Server: 又称Broker,接收客户端的连接,实现AMQP实体服务
- Connection: 连接,应用程序与Broker的连接
- **Channel**: 网络信道,所有操作都在Channel中进行,包括定义Exchange/定义Queue/绑定Exchange和Queue/发布消息等.客户端可以简历多个Channel,每个Channel代表一个会话任务.
- Message: 消息,服务器和应用程序之间传递的数据,由properties和body组成,properties对消息进行修饰,比如消息优先级,延迟等高级特性.body就是消息体内容.
- Virtual Host: 虚拟主,用户进行逻辑分离,最上层的消息路由.:一个虚拟主机包含一组交换机,队列和绑定.默认虚拟主机 "/"
- **交换机(Exchange)**:用于转发消息.根据**路由键**的规则决定发到哪个队列.以下四种类型:
  - Direct(默认,直连交换机): direct 类型的行为是”先匹配, 再投送”. 即在绑定时设定一个 **routing_key**, 消息的**routing_key** 匹配时, 才会被交换器投送到绑定的队列中去.
  - Topic(主题交换机):按规则转发消息(最灵活). *匹配一个单词, #匹配一个或多个单词, `.`是一个分隔符.
  - Headers() : 设置 header attribute 参数类型的交换机
  - Fanout(扇形交换机): 转发消息到所有的绑定队列中,没有任何判断,适合广播类的操作.
- **Queue,队列**:消息队列,保存消息并将他们转发给消费者,多个消费者可以同时订阅一个queue,queue会均分给消费者,不是每个消费者收到相同的消息.
- **binding,绑定**: **交换机和队列之间的绑定**,binding中可以包含route key

#### 常用操作
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