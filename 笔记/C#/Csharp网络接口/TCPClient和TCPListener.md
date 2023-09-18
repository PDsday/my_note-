![[Pasted image 20230905101207.png]]

		微软建议使用socket，socket可以或的最大控制和性能。而TCPClient和TCPListener属于帮助程序类。这两个类其实也是在Socket类上再封装得来的。
		这两个类构建的通信通道仅用于TCP,NetwortStream类的网络。

----

### 1、创建TCPClient

```C#
TCPClient client = new TCPClient();
```

		TCPClient实际上是内部调用Socket的构造函数，函数的重载类型为：
		Socket（SocketType，ProtocolType）;
		函数会创建尝试双堆栈套接字，如果不支持IPv6，则放弃转而回退到普通堆栈的IPv4。
		当然，用户可以指定创建哪一种：

```C#
//指定IP家族协议的重载
using var client = new TcpClient(AddressFamily.InterNetWork);
```

		socket创建套接字后，需要依据IPEndPoint去绑定IP和端口号，而TCPClient，可以再构造函数时，直接传入这个IPEndPoint，实质上等同于，创建套接字后，条用Bind方法。

```C#
// Example IPEndPoint object
var endPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 5001);
using var socket = new Socket(endPoint.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
socket.Bind(endPoint);

//等价于如下：
var endPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 5001);
using var client = new TcpClient(endPoint);

```

		创建套接字或者TCPClient，还可以使用字符串去链接一些常用的端口和host，host万维网网址以字符串方式给出：

```C#
using var client = new TcpClient();
client.Connect("www.example.com", 80);

//等同于如下

using var socket = new Socket(SocketType.Stream, ProtocolType.Tcp);
socket.Connect("www.example.com", 80);

```

----

### 2、创建TCPListener
```C#
var listener = new TcpListener(IPAddress.Loopback, 5000);

//IPAddress.Loopback，本地回环地址，127.0.0.1

//等效于
var ep = new IPEndPoint(IPAddress.Loopback, 5000);
using var socket = new Socket(ep.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
```

		在实例化类的原理其实和TCPClient没什么区别，主要是监听和链接套接字的过程。
		在TCPListener中，监听套接字的过程为由Listen方法，改为Start方法。

```C#
var listener = new TcpListener(IPAddress.Loopback, 5000);
listener.Start(10);

//等效于

var endPoint = new IPEndPoint(IPAddress.Loopback, 5000);
using var socket = new Socket(endPoint.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
socket.Bind(endPoint);
try
{
    socket.Listen(10);
}
catch (SocketException)
{
    socket.Dispose();
}

```

		同样，建立链接后，会创建对等套接字。

```C#
 TcpClient client = tcpListener.AcceptTcpClient();

// Synchronous alternative.
// var acceptedSocket = listener.AcceptSocket();
```



### 3、数据收发
		数据收发需要使用NetWorkStream，使用过程如下：

```C#

//一个客户端的发送过程，服务端吧TCPClient修改为TCPListener就好
TcpClient tcpClient = new TcpClient();
tcpClient.Connect("127.0.0.1", 9000);
NetworkStream netStream = tcpClient.GetStream();

int Len = 1024;
byte[] datas = new byte[Len];


netStream.Write(datas, 0, Len);               


//接收过程，客户端和发送端都是相同的
  byte[] data = new byte[1024];
   while (true)
   {
         if (stream.DataAvailable)
         {
                int len = stream.Read(data, 0, 1024);
                Console.WriteLine($"From:{remote}:Received ({len})");
          }
   }
```

### 完整的例程

######  1、服务器历程

```C#
 static void Main()
 {
     Console.WriteLine("Hello World");

     //1.这里已经包含的创建套接字以及绑定套接字
     TcpListener tcpListener = new TcpListener(IPAddress.Parse("127.0.0.1"), 8080);

     //2.等价于监听套接字
     tcpListener.Start();

     TcpClient tcpClient;
     //3.等待链接
     while (true)
     {
         if (tcpListener.Pending()) //如果由节点通过三次握手挂在链表下了
         {
             tcpClient = tcpListener.AcceptTcpClient();
             break;
         }
     }

     NetworkStream msg_stream = tcpClient.GetStream();
     

     while (true)
     {
         if (msg_stream.DataAvailable) //对等套接字数据流可被读
         {
             byte[] data = new byte[1024];
             int len = msg_stream.Read(data, 0, 1024);
             string str = Encoding.UTF8.GetString(data, 0, len);
             
             Console.WriteLine(str);
         }
     }
 }
```


![[Pasted image 20230905111346.png]]

###### 2、客户端完整历程

```C#
 static void Main()
 {
     Console.WriteLine("Hello World");

     IPEndPoint ipendpoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);

     TcpClient tcpClient = new TcpClient();

     do 
     {
         tcpClient.Connect(ipendpoint);
     }while (!tcpClient.Connected);

     string str = "hahahaha";
     byte[] datas = Encoding.UTF8.GetBytes(str);

     NetworkStream stream = tcpClient.GetStream();
     int Len = datas.Length;

     
     while (true)
     {
         stream.Write(datas, 0, Len);
         Thread.Sleep(1000);
     }
   

 }
```


![[Pasted image 20230905112051.png]]