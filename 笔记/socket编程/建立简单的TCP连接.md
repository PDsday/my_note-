>socket编程建立简单的TCP连接，其服务端分为4步，分别是：
>创建套接字->绑定套接字->监听套接字->连接套接字；
>一个简单的客户端则直接，创建套接字->连接套接字；


---
### 一个创建客户端的例程：
```c
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <sys/types.h>          
#include <sys/socket.h>

/*
    创建，绑定，监听，链接
*/
int server_fd; 
struct sockaddr_in server_info;
struct sockaddr_in client_info;

int len = sizeof(struct sockaddr_in);

int main(int argc, char const *argv[])
{
    server_fd = socket(AF_INET,SOCK_STREAM,IPPROTO_IP);//(IPv4,数据流，IP协议)
    
    server_info.sin_family = AF_INET;
    server_info.sin_port = htons(8080);
    server_info.sin_addr.s_addr = htonl(INADDR_ANY);//通过本地任意网卡的IP都能接上8080端口

    

    bind(server_fd,(struct sockaddr *)&server_info,sizeof(server_info));

    listen(server_fd,3);

    while(1)
    {
        int accept_ret = 0;
        accept_ret =  accept(server_fd,(struct sockaddr*)&client_info,&len);
        printf("usr:%s link\n",inet_ntoa(client_info.sin_addr));
       
        break;
    }

    return 0;
}

```

### 一个创建服务端的例程
```c
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <sys/types.h>          
#include <sys/socket.h>

int clien_fd; 

struct sockaddr_in server_info;
struct sockaddr_in client_info;
int len = sizeof(server_info);


#define server_ip "10.55.123.161"
#define server_port 8080

int main(int argc, char const *argv[])
{
    clien_fd = socket(AF_INET,SOCK_STREAM,0);

    server_info.sin_addr.s_addr = inet_addr(server_ip);
    server_info.sin_port = htons(8080);
    server_info.sin_family = AF_INET;

    connect(clien_fd(structsockaddr*)&server_info,sizeof(server_info)); 

   // getsockname(connect_ret,(struct sockaddr*)&client_info,&len);
    getpeername(clien_fd,(struct sockaddr*)&client_info,&len);

    printf("link:%s\n",inet_ntoa(client_info.sin_addr));

    return 0;
}
```


---
# API

### 1、socket
_man手册的描述为：_
![[Pasted image 20230227174038.png]]

		这个函数作用就是创建一个套接字，并为这个套接字指定，它将使用的家族协议，传输的数据类型，协议等等。
		家族协议一般是两种，IPv4 和 IPv6;
		1、IPv4 则填入 AF_INET;
		2、IPv6 呢填入 AF_INET6;
	  
	   传输类型只有两种，数据流，或者数据报，对应着传输层采用TCP还是UDP协议。
	   数据流 SOCK_STREAM
	   数据包 SOCK_DGRAM
	   如果不使用TCP/IP，它还有其他的数据结构形式

	 传输协议一般写0 ，0标识IP协议，这个数字和IP头部报文中的protocol位是一个意思。

_这个函数创建的套接字，并没有绑定上IP和端口号，绑定的工作将由另一个函数_

### 2、bind
_man手册的描述为：_
![[Pasted image 20230227175443.png]]

		这个函数可以简单的理解为，给刚创建的套接字绑定源端口号和源IP。其中绑定的结构体有三种：
		1、struct sockaddr
		2、struct sockaddr_in
		3、struct sockaddr_in6
		但是这三个结构体，传入函数都要先强制转化成 struct sockaddr。但三者的实际长度不能按照 struct sockaddr来解析，因此还需要最后一个参数，len，来辨明传入的结构体真正的大小。
		而sockaddr和sockaddr_in是等大的，因此这个len参数主要用在传入是sockaddr_in6的时候。

---
#### sockaddr
这个结构体是函数默认的结构体，可以观察它的组成：
![[Pasted image 20230228090829.png]]
可以看到，第一个参数为 SOCKADDR_COMMON ,这是一个宏定义，实际上描述的是一个变量，用来描述套接字使用的家族协议。
![[Pasted image 20230228090950.png]]
![[Pasted image 20230228091024.png]]

char sa_data[14] 字符数组，则是将目标端口和信息端口混在了一起。计算机处理时，必须将两个信息分开。

---
#### sockaddr_in
这个结构体解决的上一个结构体的缺陷，可以观察它的组成
![[Pasted image 20230228091613.png]]
首先，依旧是描述套接字的家族协议：SOCKADDR_COMMON
然后，该结构提将端口号，地址号分开描述，分别是：
sin_port  参数类型为 in_port_t  其原型是 unsigned short int 占用2个字节
sin_addr  参数类型为 in_addr  其原型是  unsigned long int 占用 4个字节

可见，这个sockaddr_in 结构体，在大小上，就比sockaddr 小了一部分
前者，分配给端口以及IP 为char 14个字节，后者仅仅分配了6个字节。而底层解析，是按照sockaddr的大小进行解析的。因此，sockaddr_in的最后一个部分，负责用0补全空余的字节。因此描述为

sin_zero  =  sizeof(struct sockaddr) - sizeof(in_port) - sizeof(struct in_addr);

---

#### sockaddr_in6
这个结构体的组成和 sockaddr_in是类似的，但问题在于，in6面向IPv6的地址，这种地址组成需要128个位，也就是 16个字节，远远大于了 sockaddr。这个目前用不到，不解析。


---
## 3、listen
		该函数也称为监听套接字。要理解该函数，需要回顾TCP协议的三次握手。
		在linux内核中，会维护连个队列：分别是 SYN队列，和Accpeet队列。
		前者，是指收到SYN请求连接，处于SYN_RCVD的客户端；
		后者，是已经完成三次握手的，处于以建立连接（ESTABLISHED）状态的客户端；
		当服务器收到SYN报文时，会将其加入到内核的 SYN队列，接着发送SYN+ACK个客户端，等待客户端回应ACK报文。等到客户端回应，服务器收到ACK报文后，就将其移动到Accept队列。这部分，是API--accpet完成的。

该函数在man手册中的描述为：
![[Pasted image 20230228093630.png]]

		  最后的参数 backlog，在历史版本的描述中有一定的变化，在以前，这个参数被认为是SYN队列的大小，也有认为是accpet对列的大小，还说是两个队列总和的大小值。
		  实测下来，感觉因该是指，同一时刻，未完成三次握手时，允许接入的客户端数量。也就是SYN队列的带下。
		  Berkeley的实现给backlog增设了一个模糊因子：把它乘以1.5得到的未处理队列最大长度。举例来说，通常指定为5的backlog值实际上允许最多有8项在排队。也就是最多允许8个客户端同时发起TCP握手。
		  如果不想对方接上客户端，关掉套接字，而不要把这个参数设置为0，因为不同的操作系统，对这个参数的解析时不同的。Linux下，即使这个参数是0，依旧允许同时3个客户端建立握手。


---
## 4、accept
		Accept函数这个也是只有TCP服务器才会调用到的函数，用于从已完成连接对接对头返回下一个已完成的连接，如果说已完成的连接对接为空，那么进程就会被这个函数所阻塞，从而休眠。对应函数的定义入下：

![[Pasted image 20230228095155.png]]

_它的描述相当复杂，可以直接理解成，将接入的客户端的对等套接字返回，同时，也可用一个结构体去获得客户端的IP和端口信息_

---

## 5、connect
		这个函数只有客户端会调用，用来连接一个指定IP，端口的服务器。

![[Pasted image 20230228095414.png]]


