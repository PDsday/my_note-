		stm32也可以写bootloader，利用串口对程序进行升级，拜托下载器。BootLoader则要预先写好在程序中。

#### 1、分配Flash
> STM32的启动过程，上电后，系统PC指针从0x00000000开始运行，跳转，作为引导程序的bootloader，则要引导pc指针指向应用程序所在的地址。因此，要对stm32的启动过程，Flash分配有足够的了解。

> 目前手上有一块F103c6t6的最小系统板，它的 内部Flash大小为32KB;
> 常用的F405RG，内部Flash大小为1M。
> 内部Flash是程序存储的地方。

###### 1.2、回顾下stm32的启动
		stm32的启动和boot引脚的选择有关。

![[Pasted image 20231205091526.png]]
		1、一般都是以主闪存存储器启动，也就是内置Flash，使用这种方式启动，尝尝用于SWD，JTAG调试。
		2、一般要实现BootLoader，可选择系统存储器启动，将程序写入内部Flash中。

		其实如果bootloader程序没有很大，且芯片的SRAM大小也比较充足的话，可以把BootLoader和应用程序都放在内置Flash中。方式2其实最适合bootloader，但如果采用此方式，刷机后需要手动重启，更改跳线帽等。

###### 1.3、bootloader的主要任务
		bootloader本身只是一小块代码，因此它的工作其实并不多。主要如下：
![[Pasted image 20231205093843.png]]
而程序流程如下：
![[Pasted image 20231205093936.png]]