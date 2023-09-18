		套接字是IP和端口号所组合的一个抽象概念，C#同样提供了socket套接字编程所需要的一系列接口。

### 1、创建套接字
		无论是客户端还是服务端，创建套接字方法必不可少。一个常见的socket对象创建方式如下：

```C#
using System.Net.Socket;
Socket s = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
```

		这一个例子创建了一个监控ipv4，tcp类型的套接字。

----

	套接字在C#中，和C++一样被封装成了一个类，它的构造函数有三个重载：

```C#
public Socket(SocketInformation socketInformation);
public Socket(SocketType socketType, ProtocolType protocolType);
public Socket(AddressFamily addressFamily, SocketType socketType, ProtocolType protocolType);

//其实，SocketInformation 是一个包含 SocketType和ProtocolType的结构体罢了，因此，第一个重载方法和第二个重载方法是一样的。
```


### 2、绑定套接字
		C#中同样使用Bind()方法绑定套接字，但它声明绑定的是一个IPEndPoint对象。

```C#
public void Bind (System.Net.EndPoint localEP);
```

		首先，Bind是位于Socket类里的方法，创建Socket类时，我们初始化了地址类型，协议类型，数据流控方式，但是并没有指定一个IP和端口。要直到，Socket就是IP和端口的结合所抽象出来的，这个抽象的任务，交给了IPEndPoint。
		可以这么理解，C#将这样一个网络节点，认为是数据包交付的终点，因而称为IPEndPoint。因此，一个创建+绑定的过程应当如下：

```C#
//创建套接字
Socket serverSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

//将字符串转化为网络端格式
IPAddress iP = IPAddress.Parse("127.0.0.1");

//创建IPEndPoint
IPEndPoint iPEndPoint = new IPEndPoint(iP, 2300);//2300是端口号

//调用socket的绑定方法
serverSocket.Bind(iPEndPoint);
```

		如上述，就完成了套接字的绑定。


### 3、链接套接字
		如果是客户端，创建套接字后，不必绑定套接字，而是直接Connect客户端即可。Connect也是Socket的方法之一，它负责建立两个socket之间的链接。同样的，Connect需要提供一个IPEndPoint，帮助数据包在茫茫网络找到目标。
		链接过程如下：

```C#
IPAddress iP = IPAddress.Parse("127.0.0.1");

IPEndPoint iPEndPoint = new IPEndPoint(iP, 2300);

Socket serverSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);　　　　　　　　//创建与远程主机的连接
serverSocket.Connect(iPEndPoint);
```

### 4、监听套接字
		监听概念和C语言的监听相同，所有发起链接请求的套接字会在三次握手后，挂在内核管理的一条链表下，排队逐个被服务套接字处理。Listen也是套接字的方法之一。

```C#
//原型
public void Listen (int backlog);

//使用
serverSocket.Listen(10);    //开始监听

```

### 5、Accept（）接收请求

		面对已经位于监听链表下的对等套接字节点，通过Accpet请求进行正式的套接字同话。完成Accpet将会返回一个对等套接字，双方通过对等套接字进行通讯。

```C#
 Socket temp = serverSocket.Accept();//为新建连接创建新的socket**
```

### 6、数据的收发
		Receive和Send用于数据的收发，他们是Socket封装好的方法之一。也有相当多的重载方法。这里列出常用形式

```C#
//接收过程

//接收数组
 byte[] recvBytes = new byte[1024];
 int bytes;
//指定接收数据存放位置，接收长度，接收缓冲区位置偏移量 
 bytes = temp.Receive(recvBytes, recvBytes.Length, 0);

//将接收到的数据转化成字符串方便打印调试终端
 string recvStr = "";
 recvStr += Encoding.ASCII.GetString(recvBytes, 0, bytes);



//发送过程
   string str = "hello";
   //将字符串转化成字节流
   byte[] a = Encoding.UTF8.GetBytes(str);
   //送出，字节流偏移0
   send = socket.Send(a, 0);
```

		除此之外，还有Read和Write方法进行读写。

### 服务端完整实现流程

```C#
 static void Main()
 {
     Console.WriteLine("Hello World");

     //创建套接字
     Socket server = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

     IPEndPoint ipendpoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);

     //绑定套接字
     server.Bind(ipendpoint);

     //监听套接字
     server.Listen(10);

     //链接套接字
     Socket client;

     while (true)
     { 
         client = server.Accept();//在建立起链接前，程序会一致堵塞在这里
         break;
     }

     //创建一个数据存储数组
     byte[] msg = new byte[1024];
     int num;
     while (true)
 { 
     num = client.Receive(msg,msg.Length,0);
     if (num == 0)
     {
         Console.WriteLine("client离线");
         break;
     }
     string recvStr = "";
     recvStr += Encoding.ASCII.GetString(msg, 0, num);
     Console.WriteLine(recvStr);
 }

 client.Close();
 server.Close();
 }
```

![[Pasted image 20230905094952.png]]


### 客户端实现方式


```C#
static void Main()
{
    Console.WriteLine("Hello World");

    //创建套接字
    Socket client = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

    IPEndPoint ipendpoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);

    bool ConnectState;
    //链接套接字
    do
    {
        client.Connect(ipendpoint);

        ConnectState = client.Connected; //获取链接的状态
    } while (ConnectState==false);

    while (true)
    {
        string str = "hello";
        byte[] a = Encoding.UTF8.GetBytes(str);
        client.Send(a, a.Length,0);
    }

}
```

![[Pasted image 20230905100336.png]]