## Channel,EventLoop,ChannelFuture

- channel-socket
- eventloop-控制流,多线程处理,并发
- channelFuture-异步通知

### EventLoop接口

channel/eventLoop和eventLoopGroup之间关系:

- 一个eventLoopGroup包含一组eventLoop
- 一个eventLoop在他的生命周期只和一个Thread绑定
- 所有由eventLoop处理的IO事件都会由和他绑定的Thread处理.
- 一个Channel在他的生命周期只和一个eventLoop绑定
- 一个eventLoop可以被分配一个或多个Channel