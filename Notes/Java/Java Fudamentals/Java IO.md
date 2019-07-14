# Java IO

* BIO 同步阻塞IO，服务器实现模式为一个连接一个线程 ，可以用线程池改善不过仍然严重依赖线程
* NIO (Non-blocking I/O , New I/O) 同步非阻塞IO模型，是IO多路复用的基础。服务器实现模式为一个请求一个线程，即客户端发送的**连接请求都会注册到多路复用器**上，多路复用器轮询到连接有I/O请求（接受连接就绪）时才启动一个线程进行处理。 
* AIO 异步非阻塞 服务器实现模式为**一个有效请求一个线程**，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。 

## 阻塞&非阻塞 同步&异步

* **阻塞**：比如read，I/O操作需要彻底完成后才能返回用户空间 

* **非阻塞**： I/O操作被调用后立即返回一个状态值，无需等I/O操作彻底完成 

  非阻塞针对的是，应用API被调用后，如果数据没有加就绪，直接返回，不等待数据准备好

* **同步**： 操作系统完成数据从内核态到应用空间拷贝的过程中，进程是阻塞的

* **异步**：所有的IO读写交给操作系统，应用程序可以直接拿到数据；用户线程发起I/O请求后仍需要继续执行，当内核I/O操作完成后会通知用户线程，或者调用用户线程注册的回调函数 

* NIO是同步的原因： NIO在**IO操作（操作系统内存拷贝）**是时阻塞的，异步不会在IO操作上阻塞

* NIO是非阻塞的原因： 采用轮询方式等待就绪

* 阻塞 非阻塞 针对的是数据准备阶段（数据有没有拷贝到kernal的page cache）

* 同步 异步 针对的是操作系统数据拷贝阶段（从kernal拷贝到应用层）



* 传统的BIO多线程—每连接每线程模型

  ```
  {
   ExecutorService executor = Excutors.newFixedThreadPollExecutor(100);//线程池
  
   ServerSocket serverSocket = new ServerSocket();
   serverSocket.bind(8088);
   while(!Thread.currentThread.isInturrupted()){//主线程死循环等待新连接到来
   Socket socket = serverSocket.accept();
   executor.submit(new ConnectIOnHandler(socket));//为新的连接创建新的线程
  }
  
  class ConnectIOnHandler extends Thread{
      private Socket socket;
      public ConnectIOnHandler(Socket socket){
         this.socket = socket;
      }
      public void run(){
        while(!Thread.currentThread.isInturrupted()&&!socket.isClosed()){死循环处理读写事件
            String someThing = socket.read()....//读取数据
            if(someThing!=null){
               ......//处理数据
               socket.write()....//写数据
            }
  
        }
      }
  }
  ```

  socket.accept() socket.read() socket.write() 都是同步阻塞的

  这个模型的缺点：`严重依赖于线程`，带来以下几点缺陷：

  * 线程的创建和销毁成本很高 
  * 线程本身占用较大内存，像Java的线程栈，一般至少分配512K～1M的空间，如果系统中的线程数过千，恐怕整个JVM的内存都会被吃掉一半。 
  * 线程的切换成本是很高的。操作系统发生线程切换的时候，需要保留线程的上下文，然后执行系统调用。 

* 常见的几种IO模型

<center> <img src="Pics\常见的io模型.jpg"/></center>

* 以socket.read()为例，BIO NIO AIO的区别

  * BIO：如果TCP RecvBuffer里面没有数据，会一直阻塞，直到数据到来，完成数据的拷贝

  * NIO：如果**TCP RecvBuffer**有数据，就把数据从网卡读到内存，并且返回给用户；反之则直接返回0，永远不会阻塞 

    等待就绪阶段（等待RecvBuffer里面有数据了）是**非阻塞**的，**真正的I/O操作是同步阻塞的**（主要是内存之间的拷贝，消耗CPU但性能非常高） 

    

  * AIO： 不但等待就绪是非阻塞的，就连数据从**网卡到内存的过程也是异步**的。 

## NIO

* Buffer 缓冲区

   buffer是一个对象，包含了读取和写入的数据，在nio中，所有的数据都是通过缓冲区来处理的。 

  * 容量（Capacity） 缓冲区能够容纳的数据元素的最大数量。
  * 上界（Limit） 
  * 位置（Position） 下一个要被读或写的元素的位置。Position会自动由相应的 get( )和 put( )函数更新。
  * 标记（Mark）一个备忘位置。用于记录上一次读写的位置。 

* channel **封装了对数据源的操作**，数据源可以是文件可以是socket

  * FileChannel 
  * SelectableChannel:  ServerSocketChannel(监听端口)  SocketChannel(TCP) DatagramChannel(UDP)

* Selector

  <center> <img src="Pics\nio.png"/></center>

  **选择器** 多路复用器 ，用于**检查一个或多个NIO Channel**（通道）的状态是否处于可读、可写。如此可以实现单线程管理多个channels,也就是可以管理多个网络连接。 

  Selector会不断地轮询注册在其上的所有channel，如果某个channel是就绪状态（**跟其对应的事件就绪**），就会被selector找出来。

  **好处**：使用更少的线程，避免了线程上下文切换的开销

  注册channel到selector:

  ```
  channel.configureBlocking(false);   //channel要是非阻塞的
  SelectionKey key = channel.register(selector, Selectionkey.OP_READ);
  ```

  SelectionKey表示selector监听channel时 关注的事件，如果channel已经满足状态就会触发selector

  * SelectionKey.OP_CONNECT   客户端连接就绪
  * SelectionKey.OP_ACCEPT      服务端接受就绪
  * SelectionKey.OP_READ          读就绪
  * SelectionKey.OP_WRITE         写就绪

* NIO的一个服务器简单实现

  ```
  ServerSocketChannel ssc = ServerSocketChannel.open();
  ssc.socket().bind(new InetSocketAddress("localhost", 8080));
  ssc.configureBlocking(false);//channel 要是非阻塞模式，因为阻塞模式下，注册操作是不允许的，会抛出 IllegalBlockingModeException 异常
  Selector selector = Selector.open();
  ssc.register(selector, SelectionKey.OP_ACCEPT);  //注册channel
  
  while(true) {
      int readyNum = selector.select();   //阻塞直到有注册的事件就绪了
      if (readyNum == 0) {
          continue;
      }
      Set<SelectionKey> selectedKeys = selector.selectedKeys();  //通过SelectionKey集合获得所有已经就绪的channel
      Iterator<SelectionKey> it = selectedKeys.iterator();
      
      while(it.hasNext()) {
          SelectionKey key = it.next();
          
          if(key.isAcceptable()) {   //判断事件是否就绪
              // 接受连接
          } else if (key.isReadable()) {
              // 通道可读
          } else if (key.isWritable()) {
              // 通道可写
          }
          
          it.remove();
      }
  }
  ```

## AIO

* JDK1.7后引入

* IO也变成异步，而不是跟NIO一样在IO准备好时通知线程IO操作还是异步阻塞，AIO在IO操作完成之后（**操作系统完成**）才会给线程发通知。等读写完成过调用**回调函数**进行业务处理

* CompletionHandler，**回调接口**，在socket进行accept/connect/read/write等操作时，可以传入一个CompletionHandler的实现，操作执行完毕后，会调用注册的CompletionHandler。 

* AIO的异步对于文件IO以及socket都适用

* 引入4个异步的channel

  * AsynchronousSocketChannel
  * AsynchronousServerSocketChannel
  * AsynchronousFileChannel
  * AsynchronousDatagramChannel

* 回调式的异步

  基本思想是主线程会派一个侦查员**CompletionHandler**到独立的线程中执行IO操作。这个侦查员将带着IO的操作的结果返回到主线程中，这个结果会触发它自己的**completed**或**failed**方法 

## AIO NIO

* NIO适用于连接数目多且连接比较短（轻操作）的架构，不适用于读写过程长的任务
* AIO方式使用于连接数目多且连接比较长（重操作）的架构 ，适用于读写过程长的任务
* BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高 

## Unix/Linux的五种IO模型

* IO模式

  * 大多数文件系统默认的IO方式都是缓存IO，操作系统会将IO的数据缓存在文件系统的页缓存中（Page Cache）
  * 以read操作为例，IO操作会经历**两个阶段**：**等待数据准备(应用API)**、**将数据从内核拷贝到进程（操作系统IO）**（用户空间）中

* Linux常见的五种IO模式

  * 阻塞IO
  * 非阻塞IO
  * IO多路复用
  * 信号驱动IO（不常用）
  * 异步IO

* 阻塞IO

  blocking IO的特点就是在IO执行的两个阶段都被block了 

  用户进程（线程）阻塞直到数据被拷贝到用户态

* 非阻塞IO

  nonblocking IO的特点是用户进程需要**不断的主动询问**kernel数据好了没有。

  在IO的第一个阶段，如果数据没有准备好，那么用户进程不会阻塞，而是返回一个状态，在第二个阶段进程阻塞

* IO多路复用 

  multiplexing Linux中的`select 、poll、epoll`都属于多路复用的范畴；Java NIO就属于这个范畴

  单个进程可以同时处理多个IO连接，`select 、poll、epoll`会不断轮询绑定的socket，如果某个socket状态就绪，就通知用户进程

* 异步IO

  asynchronous IO

  用户进程在调用API后直接返回，不阻塞，数据拷贝到用户态以后再通知进程处理

* 同步：blocking IO，non-blocking IO，IO multiplexing都属于synchronous IO 

## Reactor模式 Proactor模式

* Reactor模式是事件驱动模型，有一个或多个并发输入源，有一个Service Handler，有多个Request Handlers；这个Service Handler会同步的将输入的请求（Event）多路复用的分发给相应的Request Handler。 

  Reactor实现同步IO多路分发，Proactor实现异步IO的分发

* Proactor模式，

## Reference

* [Java NIO解析 美团技术团队](https://zhuanlan.zhihu.com/p/23488863)
* [NIO selector 掘金](https://juejin.im/post/5afad7b1f265da0b7f44b60b#heading-3)
* [Java AIO 掘金](https://juejin.im/entry/583ec2e3128fe1006bfa6c83)
* [Java新一代网络编程模型AIO原理及Linux系统AIO介绍 ](http://www.52im.net/thread-306-1-1.html)
* [Java AIO编程](https://colobu.com/2014/11/13/java-aio-introduction/)
* [Linux IO模式及 select poll epoll](https://juejin.im/entry/5971c35a6fb9a06bc6045e61)