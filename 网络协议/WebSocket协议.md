# websocket协议

## http协议相关

- http1 一次链接 一个请求一个响应
- http1.1可以一次连接,发送多个请求

#### connect 属性

- keep-alive 复用连接
- close 连接关闭
- upgrade  升级

#### upgrade 属性

## ws实现了客户端和服务器的全双工通信(full-duplex)-都可以主动给对方发数据
> 浏览器和服务器只需要要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。在此WebSocket 协议中，为我们实现即时服务带来了两大好处：
> 1. Header  互相沟通的Header是很小的-大概只有 2 Bytes
> 2. Server Push

* 握手阶段使用http协议,握手完成之后使用ws协议
* WebSocket是一种二进制协议