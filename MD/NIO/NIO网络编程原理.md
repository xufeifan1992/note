## NIO网络编程原理







### NIO网络编程模型



#### NIO简介

* 姓名: Non-blocking I/O(非阻塞IO) 或者 New I/O
* 出生: JDK1.4
* 职务:高并发网络服务器支持岗

#### 编程模型

* 模型:对事物共性的抽象
* 编程模型:对编程共性的抽象

##### BIO网络模型

1.服务端:监听建立连接请求

2.客户端:发起请求建立连接请求

3.服务端:启动新线程

4.服务端:线程响应客户端

5.服务端:服务端等待客户端再次请求



![](https://raw.githubusercontent.com/xufeifan1992/note/master/images/picgo-BIO网络模型2.png)



![](https://raw.githubusercontent.com/xufeifan1992/note/master/images/picgo-阻塞.png)

##### BIO网络模型缺点

* 阻塞式I/O模型
* 弹性伸缩能力差
* 多线程消耗资源





### NIO网络编程详解

#### NIO网络模型

1.注册建立连接事件，循环检测注册时间就绪情况

2.发送建立连接请求

3.启动建立连接事件处理器

4.创建与客户端连接

5.响应客户端建立连接请求

6.注册连接可读事件

7.发送请求

8.启动连接读写处理器

9.处理与客户端读写业务

10.响应客户端请求

11.注册连接可读事件





* 非阻塞式I/O模型
* 弹性伸缩能力强
* 单线程节省资源

#### NIO核心

* Channel(通道)
  * 双向性
  * 非阻塞性
  * 操作唯一性
    * Channel实现
      * FileChannel(文件类)
      * DatagramChannel(UDP类)
      * ServerSocketChannel/SocketChannel(TCP类)
* Buffer(缓冲区)
  * 作用：读写CHannel中数据
  * 本质：一块内存区
    * 属性
      * Capacity：容量
      * Position：位置
      * Limit：上限
      * Mark：标记
* Selector(选择器或者多路复用器)





### NIO网络编程代码

```java
/**
 *  监听端(服务端)
 */
ServerSocket serverSocket = new ServerSocket(8080)

  while(true){
    Socket socket = serverSocket.accept()；
  }
/**
 *  客户端
 */
Socket socker = new Socket(127.0.0.1,8080);

/**
 * 获取输入输出流
 */
InputStream inputStream = socket.getInputStream();
OutputSteam outSteam = socket.getOutputSteam()； 
```



```java
/**
 * channel代码片段
 */
//服务端通过服务端socket创建channel
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open()；
  
  
//服务端绑定端口
serverSocketChannel.bind(new InetSocketAddress(8080));

//服务端监听客户端链接，建立socketChannel连接
SocketChannel socketChannel = serverSocketChannel.accept();

//客户端连接远程主机
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1",8080));
```





```java
/**
 * Selector代码片段
 */
//创建selector
Selector selector = Selector.open();

//将channel注册到selector上，监听读就绪事件
SelectionKey selectionKey = channel.register(selector,SelectionKey.OP_READ)；

//阻塞等待channel有就绪事件发生
int selectNum = selector.select();

//获取发生就绪事件的channel集合
Set<SelectionKey> sekectedKeys = selector.selkectedKeys();
```



* selectionKey简介

  * 四种就绪状态常亮
    * connect
    * accpet
    * read
    * write
  * 有价值的属性

* NIO编程实现步骤

  * 第一步：创建Selector

  * 第二步：创建ServerSocketChannel，并绑定监听端口

  * 第三步：将Channel设置为非阻塞模式

  * 第四步：将Channel注册到Selector上，监听连接事件

  * 第五步：循环调用Select的select方法，检测就绪情况

  * 第六步：调用selectKeys方法获取就绪channel集合

  * 第七步：判断就绪事件种类，调用业务处理方法

  * 第八步：根据业务需要决定是否再次注册监听事件，重复执行第三步

    

    



### NIO网络编程缺陷