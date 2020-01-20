# netty

> 关于Netty：
>
> Netty是一个高性能、异步事件驱动的NIO框架，它提供了对TCP、UDP和文件传输的支持，作为一个异步NIO框架，Netty的所有IO操作都是异步非阻塞的，通过Future-Listener机制，用户可以方便的主动获取或者通过通知机制获得IO操作结果。作为当前最流行的NIO框架，Netty在互联网领域、大数据分布式计算领域、游戏行业、通信行业等获得了广泛的应用，一些业界著名的开源组件也基于Netty的NIO框架构建。
> 
> netty是对Java NIO和Java线程池技术的封装

- NIO是异步非阻塞
> 使用事件机制,用一个线程把accept/读写操作/请求处理的逻辑全干了,没有任务会把线程休眠,直到下一个事件来到.这样的线程成为NIO线程

### reactor线程模型
- reactor单线程模型
- reactor多线程模型,在处理器链部分采用多线程,后端程序常用模型
> 
- reactor主从模型:多个acceptor的NIO线程用于接收客户端的连接
> 将reactor分成两部分,manReactor负责处理新的socket连接,把已经建立连接的socket分发给subReactor.subReactor负责多路分离已连接的socket,读写网络数据/业务处理
> 

### 小结
Netty是建立在NIO基础之上,在NIO之上又提供了更高层次的抽象
Accept连接可以使用单独的线程池去处理,读写操作又是另外的线程池处理

## TCP 粘包/拆包
- 粘包:发送端发包时把三个数据包粘成两个TCP包发送
- 拆包:接收端根据应用协议将两个TCP包拆成三个数据包

## netty零拷贝











# [netty视频笔记](https://v-wb.youku.com/v_show/id_XMzQ5NTE5MzEzNg==.html)

Channel是对socket的抽象,每个Channel对应一个ChannelPipline,ChannelPipline是一个包含了不同的ChannelHandler的双向链表,ChannelHandler可以处理InBound和OutBound事件

EventLoopGroup类似一个线程池,包含很多线程,用来处理分配给他们不同的Channel
EventLoop相当于Java中的Executor
EventExector相当于EventLoop



> channel 渠道
> pipline  管道
