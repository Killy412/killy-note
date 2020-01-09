# J-IM

**<font color="#dd0000">笔记内容参考 [J-IM](https://gitee.com/xchao/j-im)</font>**

### 模块

- jim-parent  管理子工程以及配置版本号等

- jim-common  客户端以及服务端公用包

- jim-server  服务端

- jim-client  客户端入门案例

- jim-server-demo  服务端案例

### 消息结构格式

1. 聊天请求消息结构
2. 鉴权请求
3. 握手请求
4. 登录请求
5. 心跳请求
6. 关闭/退出请求
7. 获取用户信息请求
8. 获取用户消息
9. ...

### j-im自定义协议结构

![image-20200109143256796](C:\Users\lxq41\AppData\Roaming\Typora\typora-user-images\image-20200109143256796.png)
- 版本号 第一个字节
- 消息标识位mask(2)  描述消息是否加密/压缩/同步发送/4字节长度
- 同步发送序列号(3~6): 存放如1200.1201等整型;如果第二个字节中第三位为0(非同步发送),不需要设置
- cmd命令码(7) : 例如聊天请求为11  登录请求为5
- 消息体长度(8~11)  消息包的length

### 客户端开发流程
- 引入jim-common包
- 定义packet包
- 定义客户端消息处理器:负责所有消息的发送(编码)/接收(解码)/业务处理
- 定义启动类-运行

### 常用类
- ImConfig 配置启动参数所需要的,例如:绑定IP/端口/是否持久化
	* heartbeatTimeout 设置服务器检测心跳超时时长参数,单位毫秒,设置12000,每隔12000/2ms检测一下客户端收发状态
	* httpConfig  http协议相关配置
	* wsConfig  webSocket协议相关配置
		* IWsMsgHandler  webSocket消息处理器
	* isStore 是否开启持久化 on/开启  off/关闭
	* readBufferSize  设置tio默认接收数据的缓冲区大小,**j-im默认1M**,t-io默认是2KB
- **ImServerGroupContext  服务配置和维护,配置线程池,确定监听端口**
- MessageHelper 持久化消息助手,j-im默认实现是redisMessageHelper
- ImServerStarter  启动类
- AioListener  处理消息的核心接口,有两个子接口:ClientAioListener(客户端需要实现)和ServerClientAioListener(服务端需要实现)
- <font color="#dd0000">CommandManager  **cmd命令处理器管理中心,管理j-im中所有cmd命令的注册/删除/获取命令处理器**</font>;主要包含以下处理器
	* AuthReqHandler  鉴权请求    -COMMAND_AUTH_REQ(3)  响应:COMMAND_AUTH_RESP(4); 
	
	- ChatReqHandler  聊天请求                      -  11  - 12 
	- CloseReqHandler  关闭请求                     - 14   -  无
	- HandshakeReqHandler  握手请求           - 1  -2 
	- HeartbeatReqHandler  心跳请求             - 13  -13
	- JoinGroupReqHandler  加入群组请求      - 7  -8
	- LoginReqHandler  登录请求                           -5  -6
	- MessageReqHandler  获取用户消息请求       -19   -20
	- UserReqHandler  获取用户信息请求              -17  -18
这些cmd处理器都继承了AbstractCmdHandler
- ServerHandlerManager(现为ProtocolHandlerManager)  服务端协议处理器管理中心
- AbstractChatProcessor  持久化消息相关

### cmd业务处理器



### 相关知识点
- (处理器)handler和process区别 **process指处理的过程,名词;handler 指处理解决问题,动词;**

### 项目启动流程-理解
1. 加载配置文件,