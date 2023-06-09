>以网络层的数据运输来看，数据包从PC发出后，会经过一个又一个的路由到达最终目的地。
>假如，PC1是一个在A类网络中的主机，他要发送到另一个A类网络下的另一个主机。那么，数据包会移动到链接两个A类网络的路由。每个A类网络下的主机有2的24次方台。通过路由去维护一个大到2的24次方的IP表是做不到的。

---
## 路由表
		按照目的地IP地址一个一个的配置路由表是不可能的，但如果按照网络地址来制作路由表，情况就会变得简单。
		假如是，有4个A类网络通过3个路由R1，R2，R3组成一个互联网：
		10.0.0.0
		20.0.0.0
		30.0.0.0
		40.0.0.0

![[Pasted image 20230224104353.png]]

		10.0.0.0下的一台主机 10.0.0.12，想要发送数据到 30.0.0.52。
		那么数据包进行IP协议封装后，会得到包含源地址和目标地址的数据包，并将数据包丢到路由。
		路由在网络中，会占用不止一个IP，这要取决于，网络得拓扑结构。比如R1,它即在网1下，有在网2下，在这种拓扑结构里，它拥有两个IP，分别是10.0.0.4，和20.0.0.7。当然，R2，R3，都是有2个IP的。

![[Pasted image 20230224104940.png]]

		当数据包被丢到R1后，目标地址会依据网络类型，进行掩码运算：
		比如目标地址 30.0.0.52，与掩码255.0.0.0运算后，得到目标所在的子网为30.0.0.0。那路由器R1怎么将这个包丢到30.0.0.0中的路由器呢？

_在R1中，维护这这样一张表。_

|目标主机所在的网络|下一跳地址|
|:---:|:---:|
|10.0.0.0|直接交付0出口|
|20.0.0.0|直接交付1出口|
|30.0.0.0|20.0.0.7(R2路由)|
|40.0.0.0|30.0.0.1(R3路由)|

		这就是路由表，记录着互联网中路由的位置，这个表的大小，仅仅取决于路由的数量，而不再是2的32次方个IP，使得维护成为可能。
		因此来自10.0.0.4的数据包，在R1中通过掩码后得到目标子网，R1一句路由表，将为数据包的下一跳选定地址，那必须是跳到R3路由了。如果R3路由忙，它会跳到R2路由，寻求处理。总之，最后一跳必定是跳到目标所在子网的路由可以是R2,也可以是R3，因为他们都能将数据包抛入30子网，完成对指定目标主机的交付。
---
## 特定主机路由
		不同于在一个局域网内的主机们共享一个或几个路由，有些主机会有自己的VIP待遇，特定主机路由。
		路由表虽然大小已经大大减少，但是一旦链接的路由器数量躲起来，查询路由表依旧是需要一点点延迟的，这个时候，在进行网络测试和调试工作的程序员会给调试的主机们，安排特定主机路由。特定主机路由表里，只存放了指定的IP地址的出入口，查表的大小变小，延迟降低，调试的速度就得到加快。
## 默认主机路由
		当一个很小的子网和internet相连接的时候，直接使用一个默认的路由就可以减少路由查表，数据跳跃的时间。比如C类网络，一个网络下只有255台主机，那么，他们只用一个默认路由接入internet就好。



---
_数据包中，有目的IP，有源IP，就是没有下一跳IP，这个跳跃的过程，并不在网络层实现，而是交由下层，通过ARP协议，获得路由的MAC地址，将地址封装如MAC帧里，跳到下一个路由的。当到达下一个路由，这些MAC帧又被解包，封装上新的MAC帧。_





