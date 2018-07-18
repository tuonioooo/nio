# 基础概念

# 一、前言 {#-}

Netty是一种可以轻松快速的开发协议服务器和客户端网络应用程序的NIO框架，它大大简化了TCP或者UDP服务器的网络编程,但是你仍然可以访问和使用底层的API,Netty只是对其进行了高层的抽象。

Netty的简易和快速开发并不意味着由它开发的程序将失去可维护性或者存在性能问题。Netty是被精心设计的，它的设计参考了许多协议的实现，比如FTP，SMTP，HTTP和各种二进制和基于文本的传统协议，因此 Netty成功的实现了兼顾快速开发，性能，稳定性，灵活性为一体，不需要为了考虑一方面原因而妥协其他方面。

![](http://upload-images.jianshu.io/upload_images/5879294-684c74a0884a5815?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "image")



# 二、基础概念 {#-}

Channel也就是通道，这个概念是在JDK NIO类库里面提供的一个概念，JDK中其实现类有客户端套接字通道java.nio.channels.SocketChannel和服务端监听套接字通道java.nio.channels.ServerSocketChannel，Channel的出现是为了支持异步IO操作，JDK里面的通道是java.nio.channels.Channel。

io.netty.channel.Channel是Netty框架自己定义的一个通道接口，Netty实现的客户端NIO套接字通道是NioSocketChannel，提供的服务器端NIO套接字通道是NioServerSocketChannel。

* NioSocketChannel
 
  客户端套接字通道，内部管理了一个Java NIO中的java.nio.channels.SocketChannel实例，用来创建SocketChannel实例和设置该实例的属性，并调用Connect方法向服务端发起TCP链接等。
* NioServerSocketChannel
 
  服务器端监听套接字通道，内部管理了一个Java NIO中的java.nio.channels.ServerSocketChannel实例，用来创建ServerSocketChannel实例和设置该实例属性，并调用该实例的bind方法在指定端口监听客户端的链接。
* Channel与socket的关系
 
  在Netty中Channel有两种，对应客户端套接字通道NioSocketChannel，内部管理java.nio.channels.SocketChannel套接字，对应服务器端监听套接字通道NioServerSocketChannel，其内部管理自己的java.nio.channels.ServerSocketChannel套接字。也就是Channel是对socket的装饰或者门面，其封装了对socket的原子操作。
* EventLoopGroup
 
  Netty之所以能提供高性能网络通讯，其中一个原因是因为它使用Reactor线程模型。在netty中每个EventLoopGroup本身是一个线程池，其中包含了自定义个数的NioEventLoop,每个NioEventLoop是一个线程，并且每个NioEventLoop里面持有自己的selector选择器。
 
  ^^
 
  在Netty中客户端持有一个EventLoopGroup用来处理网络IO操作，在服务器端持有两个EventLoopGroup，其中boss组是专门用来接收客户端发来的TCP链接请求的，worker组是专门用来具体处理完成三次握手的链接套接字的网络IO请求的。
* Channel 与 EventLoop 的关系
 
  Netty中NioEventLoop是EventLoop的一个实现，每个NioEventLoop中会管理自己的一个selector选择器和监控选择器就绪事件的线程；每个Channel只会关联一个NioEventLoop；
 
  ^^
 
  当Channel是客户端通道NioSocketChannel时候，会注册NioSocketChannel管理的SocketChannel实例到自己关联的NioEventLoop的selector选择器上，然后NioEventLoop对应的线程会通过select命令监控感兴趣的网络读写事件；
 
  ^^
 
  当Channel是服务端通道NioServerSocketChannel时候，NioServerSocketChannel本身会被注册到boss EventLoopGroup里面的某一个NioEventLoop管理的selector选择器上，而完成三次握手的链接套接字是被注册到了worker EventLoopGroup里面的某一个NioEventLoop管理的selector选择器上；
 
  ^^
 
  需要注意是多个Channel可以注册到同一个NioEventLoop管理的selector选择器上，这时候NioEventLoop对应的单个线程就可以处理多个Channel的就绪事件；但是每个Channel只能注册到一个固定的NioEventLoop管理的selector选择器上。
* ChannelPipeline
 
  Netty中的ChannelPipeline类似于Tomcat容器中的Filter链，属于设计模式中的责任链模式，其中链上的每个节点就是一个ChannelHandler。在netty中每个Channel有属于自己的ChannelPipeline，对从Channel中读取或者要写入Channel中的数据进行依次处理,如下图是netty源码里面的一个图：
 
  ![](http://upload-images.jianshu.io/upload_images/5879294-cd33cbecb377b622?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "image")
  需要注意一点是虽然每个Channel（更底层说是每个socket）有自己的ChannelPipeline，但是每个ChannelPipeline里面可以复用一个ChannelHandler。

# 三、总结 {#-}

Netty作为高性能异步通讯框架，其应用还是比较广泛的，比如阿里巴巴开源的高性能Rpc框架Dubbo的网络通讯默认实现使用的是Netty， 蚂蚁金服开源的金融级Sofa-Bolt 框架，底层网络通讯也是基于 Netty 来实现的，还有最近刚开源的Zuul2.0使用Netty重写了其接受与处理请求的逻辑。

