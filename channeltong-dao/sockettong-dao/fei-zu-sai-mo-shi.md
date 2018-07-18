# 非阻塞模式

Socket 通道可以在非阻塞模式下运行。这个陈述虽然简单却有着深远的含义。传统 Java socket的阻塞性质曾经是 Java 程序可伸缩性的最重要制约之一。非阻塞 I/O 是许多复杂的、高性能的程序构建的基础。

要把一个 socket 通道置于非阻塞模式，我们要依靠所有 socket 通道类的公有超级类：

SelectableChannel。下面的方法就是关于通道的阻塞模式的：

```
public abstract class SelectableChannel
extends AbstractChannel
implements Channel
{
// This is a partial API listing
public abstract void configureBlocking (boolean block)
throws IOException;
public abstract boolean isBlocking( );
99
public abstract Object blockingLock( );
}
```

有条件的选择（readiness selection）是一种可以用来查询通道的机制，该查询可以判断通道是否准备好执行一个目标操作，如读或写。非阻塞 I/O 和可选择性是紧密相连的，那也正是管理阻塞模式的 API 代码要在 SelectableChannel 超级类中定义的原因。SelectableChannel 的剩余 API 将在第四章中讨论。

设置或重新设置一个通道的阻塞模式是很简单的，只要调用 configureBlocking\( \)方法即可，传递参数值为 true 则设为阻塞模式，参数值为 false 值设为非阻塞模式。真的，就这么简单！您可以通过调用 isBlocking\( \)方法来判断某个 socket 通道当前处于哪种模式：

```
SocketChannel sc = SocketChannel.open( );
sc.configureBlocking (false); // nonblocking
...
if ( ! sc.isBlocking( )) {
doSomething (cs);
}
```

服务器端的使用经常会考虑到非阻塞 socket 通道，因为它们使同时管理很多 socket 通道变得更容易。但是，在客户端使用一个或几个非阻塞模式的 socket 通道也是有益处的，例如，借助非阻塞socket 通道，GUI 程序可以专注于用户请求并且同时维护与一个或多个服务器的会话。在很多程序上，非阻塞模式都是有用的。

偶尔地，我们也会需要防止 socket 通道的阻塞模式被更改。API 中有一个 blockingLock\( \)方法，该方法会返回一个非透明的对象引用。返回的对象是通道实现修改阻塞模式时内部使用的。只有拥有此对象的锁的线程才能更改通道的阻塞模式（对象的锁是用同步的 Java 密码获取的，它不同于我们在 3.3 节中介绍的 lock\( \)方法）。对于确保在执行代码的关键部分时 socket 通道的阻塞模式不会改变以及在不影响其他线程的前提下暂时改变阻塞模式来说，这个方法都是非常方便的。

```
Socket socket = null;
Object lockObj = serverChannel.blockingLock( );
// have a handle to the lock object, but haven't locked it yet
// may block here until lock is acquired
synchronize (lockObj)
{
// This thread now owns the lock; mode can't be changed
boolean prevState = serverChannel.isBlocking( );
100
serverChannel.configureBlocking (false);
socket = serverChannel.accept( );
serverChannel.configureBlocking (prevState);
}
// lock is now released, mode is allowed to change
if (socket != null) {
doSomethingWithTheSocket (socket);
}
```



