## 网络分层TCP/IP

1. 应用层,代表HTTP/DNS/FTP. 定义的是应用进程间的通信和交互规则.

2. 传输层,代表TCP/UDP. 主要任务是负责向两台主机进程之间的通信提供通用的数据传输服务.

  TCP: 面向连接的可靠传输协议. 传输效率偏低.
  UDP: 无连接的传输协议.尽可能的传输数据. 传输效率较高.

3. 网络层,代表IP. 两个电脑之间的通信需要经过很多链路层,也可能经过很多通信子网.网络层的任务是选择合适的网间路由和交换节点,确保数据及时传输.

4. 链路层. 两台主机之间的数据传输,总是在一段一段的链路上传输的,这就需要使用专门的链路协议.

5. 物理层.实现相邻计算机节点的比特流透明传输.

### TCP三次握手和四次挥手.

#### 握手过程

客户端.发送带有SYN标志的数据包-一次握手
服务端.发送带有SYN/ACK的数据包到客户端-二次握手
客户端.发送带有ACK标识的数据包到服务端-三次握手

三次握手的目的是为了建立可靠的通信信道.
一次握手: server确认对方发送,自己接收正常
二次握手: client确认自己发送/接收正常,对面发送/接收正常,server确认对方发送,自己接受正常
三次握手: server确认自己发送,对面接收正常
主要就是双方都确认自己和对方发送/接收都是正常的.

#### 第二次握手传回了syn,为什么还要传ack?

传回syn是为了告诉发送端,接收到的信息是你发送的信号. 传回ack是为了建立并确认从服务端到客户端的通信.

#### 使用两次握手是否可行

防止已经失效的连接请求又被发送到服务器,从而产生错误.

如果使用的是两次握手建立连接，假设有这样一种场景，客户端发送了第一个请求连接并且没有丢失，只是因为在网络结点中滞留的时间太长了，由于TCP的客户端迟迟没有收到确认报文，以为服务器没有收到，此时重新向服务器发送这条报文，此后客户端和服务器经过两次握手完成连接，传输数据，然后关闭连接。此时此前滞留的那一次请求连接，网络通畅了到达了服务器，这个报文本该是失效的，但是，两次握手的机制将会让客户端和服务器再次建立连接，这将导致不必要的错误和资源的浪费。

如果采用的是三次握手，就算是那一次失效的报文传送过来了，服务端接受到了那条失效报文并且回复了确认报文，但是客户端不会再次发出确认。由于服务器收不到确认，就知道客户端并没有请求连接。

#### 挥手过程

客户端. 发送FIN,关闭客户端到服务端的数据传送
服务端. 收到FIN,发回一个ACK,确认序号为收到的序号加一.
服务端. 发送FIN,关闭服务端到客户端的数据传送.
客户端. 受到FIN,发回一个ACK,确认序号为收到的序号加一.

### TCP如何保证传输可靠性

1. 数据被tcp分成最适合发送的数据段.
2. tcp给每个数据进行编号,接收方对数据包进行重组,有序发给应用层.
3. 校验和.tcp将保持首部和数据的校验和. 端到端的检验和,目的是检测数据在传输过程中的任何变化.如果收到段的检验和有差错,tcp会丢失这个报文段和不确认收到这个报文段.
4. tcp的接收端会丢掉重复数据
5. 流量控制.tcp连接的每一方都有固定大小的缓冲空间,tcp接收端只允许接收缓冲空间可容纳大小的数据包,当接收方来不及处理时,能提示发送方降低发送的频率,防止数据丢失.tcp使用的流量控制协议是可变大小的滑动窗口协议.
6. 拥塞控制. 当网络拥塞时,减少数据的发送.
7. ARQ协议,也是为了实现可靠传输的.基本原理是每发完一个分组就停止发送,等待确认后,再发送下一个分组.
8. 超时重传,当tcp发送一个段后,启动一个定时器,等待目的地端确认受到这个段,如果没有及时收到一个确认,发送端会重新发送.

#### ARQ协议

自动重传(Automatic Repeat-reQuest)请求是数据链路层和传输层的数据纠正协议之一.他通过使用确认和超时这两个机制,实现在不可靠的基础上实现可靠传输.如果发送方没有收到确认消息,通常会自动重传.ARQ包括停止等待ARQ协议和连续ARQ协议.

1. 停止等待ARQ协议

基本原理是每发送一个分组就停止发送,等待确认消息,如果超过超时时间还没收到确认消息,就重新发送.
接收方收到重复消息会丢弃,但同时也会发送确认消息.

2. 连续ARQ协议

可提高信道使用率.发送方维持一个发送窗口,凡位于发送窗口发的分组可连续发送出去.不需要等待对方确认.接收方采用累计确认,对按序到达的最后一个分组发送确认,表示这个分组为止所有的分组都发送成功了.
优点是信道利用率高.
缺点是无法向发送方反映出接收方已经正确收到的分组信息.

#### 滑动窗口和流量控制

TCP利用滑动窗口实现流量控制.流量控制是控制发送方发送速率,保证接收方来得及接收.接收方发送的确认报文的窗口字段可以控制发送方的窗口大小,如果设置为0,发送方不能发送东西.

#### 拥塞控制

在某段时间内,对某一资源的需求大于资源所能提供的最大值,网络性能就会变差.这种情况叫拥塞. 拥塞控制是为了防止过多的数据注入到网络中,这样就可以使网络中的路由器或链路不致过载.
为了进行拥塞控制,TCP发送方要维持一个拥塞窗口(cwnd)的变量.控制拥塞窗口的大小取决于网络的拥塞程度,并且动态变化.发送方让自己的拥塞窗口取拥塞窗口和接收方的接收窗口中的最小的一个.
TCP拥塞控制采用四种算法,慢开始/拥塞避免/快重传/快恢复.
慢开始: 主机开始发送数据时,由小到大逐渐增加窗口,也就是由小增大拥塞窗口值.
拥塞避免: 使拥塞窗口值cwnd缓慢增大.
快重传/快恢复: 可以快速恢复丢失的数据包.没有FRR,如果数据包丢失了,TCP会使用定时器暂停数据传输.有了FRR,接收方收到了不按顺序的数据包,会立即给发送方发送一个重复确认.如果发送方接收到三个重复确认,会立即重传丢失的数据段.有了FRR,不会因为数据丢失导致传输暂停.

### TCP和UDP区别

UDP在传输数据前不需要建立连接,接收方收到udp报文数据后,也不需要给出任何确认. 虽然udp连接是不可靠的,但在某些地方是有效的工作方式,例如语音/视频/直播等.

TCP是面向连接的服务. 在传输数据前必须建立连接,传输完成后要释放连接. TCP提供可靠的,面向连接的传输服务(可靠体现在传输数据前,会通过tcp三次握手建立连接,传输数据时,还会有确认,重试拥塞控制机制,传输完成,会断开连接,节省资源),同时会增加很多开销.TCP 一般用于文件传输、发送和接收邮件、远程登录等场景。



### HTTP长连接/短连接

HTTP1.0中默认使用短连接,就是每个请求完成之后就断开连接.

HTTP1.1之后,可以使用长连接. 在请求头中加入这行
```
connection: keep-alive
```
使用长连接的情况下,当一个网页打开完成后,客户端和服务端用于传输http数据的tcp连接不会关闭,客户端再次访问这个服务器时,会使用这条已经建立的连接.Keep-Alive不会永久保持连接,连接时长可以自定义.


### HTTP1.0和HTTP1.1的区别

1. 1.0默认使用短连接.1.1默认使用长连接. 1.1的持续连接有流水线方式和非流水线方式.非流水线方式指在接收到响应之后才可以继续发送下一次请求,流水线方式指在接收到响应之前就可以继续发送下一次请求.
2. HTTP1.1新增了24个响应状态码.
3. 缓存处理.HTTP1.0主要使用请求中的If-Modified-Since,Expires 来做为缓存判断的标准.HTTP1.1 则引入了更多的缓存控制策略例如 Entity tag，If-Unmodified-Since, If-Match, If-None-Match 等更多可供选择的缓存头来控制缓存策略.
4. 带宽优化及网络连接的使用.HTTP1.0中有一些资源浪费的情况,比如客户端只需要某个对象的一部分,而服务器却将整个对象带过来了,并且不支持断点续传功能.HTTP1.1则在头中增加range参数,允许只请求资源的某个部分,返回码是206.