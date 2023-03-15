
### 1、close
		该函数用于发起四次挥手。但是如果是多进程环境下，子进程或者父进程调用了close，只是当前进程关闭了这个套接字而已，其他进程不受影响，并不会发起四次挥手。
		如果客户端主动发送close，此时，会向服务器发送四次挥手。首先是给服务器一个FIN报文，随后收到该报文的服务器TCP协议栈会将EOF写入这个对等套接字的缓冲区，因此可以调用read读取到EOF,当服务器读取到这个标识，就可以调用close关闭对等套接字，给发起挥手的客户端发送一个ACK。之后完成连接断开。

### 2、getsockname/getpeername
		如果不在accept时，传入一个空结构体获得接入的客户端参数，后续如何获得呢？
		答案就是上述两个函数。用法和传入accept的参数相同。但是存在问题，就是这两个函数必须在accept后调用。传入对等套接字，就可以获得连接方的IP信息。

### 3、setsockopt
		该函数用于设置套接字的属性。有着相当多的花样

![[Pasted image 20230228101603.png]]
> 1、level参数
> 该参数影响着后续 optname 选项的类型。常见有：
> SOL_SOCKET，此时的optname可以设置：
> 												SO_BROADCAST        允许广播
> 												SO_DEBUG                   允许调试
> 												SO_LINGER                 延迟关闭连接
> 												SO_RCVBUF                接收缓冲区大小
> 												SO_SNDBUF               发送缓冲区大小
> 												SO_RCVLOWAT        接受缓冲区下限 
> 												SO_SNDLOWAT        发送缓冲区下限
> 												SO_RCVTIMEO          接收超时
> 												SO_SNDTIMEO          发送超时
    IPPROTO_IP时，optname可以有以下选项（一部分）
													IP_HDRINCL　　　　　　　在数据包中包含IP首部　　　　　　　　
													IP_OPTINOS　　　　　　　IP首部选项　　　　　　　　　　　　　　　
													IP_TTL　　　　　　　　　生存时间　　　　　　　　　　　　　　　　
    IPPRO_TCP时，optname可以有以下选项（一部分）  
													TCP_MAXSEG　　　　　　　TCP最大数据段的大小　　　　　　　　　　  
													TCP_NODELAY　　　　　　 不使用Nagle算法　　　　　　　　　　　　 




