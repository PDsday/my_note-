
#### 1、扇区分配
![[Pasted image 20231205141357.png]]

		此机器没有BootLoader，因此使用串口刷机或者下载器刷机。程序直接从boot0模式起始的0x0800 0000开始存储。

#### 2、Main函数
##### 2.1、系统初始化
![[Pasted image 20231205141741.png]]

###### 2.1.1、关闭总中断
		关闭总中断使用的是 cpu底层汇编提供的接口，这个接口在ARM芯片中很常见，用于屏蔽系统的中断响应。

![[Pasted image 20231205141758.png]]

#此接口在汇编程序中的原文描述为：
```c
;*********************************************************************************************************
;                                      CRITICAL SECTION FUNCTIONS
;
; Description : Disable/Enable interrupts by preserving the state of interrupts.  Generally speaking, the 
;               state of the interrupt disable flag is stored in the local variable 'cpu_sr' & interrupts
;               are then disabled ('cpu_sr' is allocated in all functions that need to disable interrupts).
;               The previous interrupt state is restored by copying 'cpu_sr' into the CPU's status register.
;
; Prototypes  : CPU_SR  CPU_SR_Save   (void);
;               void    CPU_SR_Restore(CPU_SR cpu_sr);
;
;
; Note(s)     : (1) These functions are used in general like this:
;
;                   void  Task (void *p_arg)
;                   {
;                                                               /* Allocate storage for CPU status register */
;                   #if (CPU_CFG_CRITICAL_METHOD == CPU_CRITICAL_METHOD_STATUS_LOCAL)
;                       CPU_SR  cpu_sr;
;                   #endif
;
;                            :
;                            :
;                       CPU_CRITICAL_ENTER();                   /* cpu_sr = CPU_SR_Save();                  */
;                            :
;                            :
;                       CPU_CRITICAL_EXIT();                    /* CPU_SR_Restore(cpu_sr);                  */
;                            :
;                            :
;                   }
;
;               (2) CPU_SR_Restore() is implemented as recommended by Atmel's application note :
;
;                      "Disabling Interrupts at Processor Level"
;*********************************************************************************************************
```

###### 2.1.2、HWInit

		此部分做片上外设初始化，包括函数为：
		1、RCC_Configuration  片上外设时钟使能；
		使用的相关资源为：GPIOA、GPIOB、GPIOC、GPIOD、DMA1、DMA2、UART1/2、ADC1、TIM8、DAC、SPI3、TIM3、TIM4。
		
		2、NVIC_Configuration 中断向量初始化；
		中断分组使用分组1，此分组分配2级抢占优先级，以及8级的响应优先级，其中有一句设定中断向量表起始地址相对程序存放地址的偏移为0，个人感觉多余。
		
		3、GPIO_Configuration GPIO初始化
		4、读取了一次DSP的状态
		4、TIM_Configuration 定时器初始化
		5、ExUart_Configuration PC端通讯串口初始化
		6、InUart_Configuration DSP端通讯初始化
		7、InUart2_Configuration 充电器ARM通讯
		8、ADC_Configuration 配置ADC
		9、SSI_Configuration SSI串行通信编码器配置

###### 2.1.3、采样，参数，以及CAN通讯初始化
![[Pasted image 20231205163012.png]]

		ReadRara为从一个存储在堆区的参数表中获取数据，但这个表的初始化函数在下一步，SwInit函数里，因此第一次读取CANAddr实际上就是读个寂寞。
		SwInit()初始化的设置也很多：
		1、ParaInit，内部参数表初始化，但经过一次修改后，参数表初始化的任务已经交给了CANopenParaInit统一配置。
		2、InitMotorOffAddr
		3、InitMotorOffPara
		4、InitErrRecord  初始化历史故障
		5、ReadErrRecord  上电读取一次历史故障到内存
		6、CANopenParaInit
		7、TCP_Para_init
		8、MotoTempMeasureInit
		9、CabinetTempMeasureInit2

###### 2.1.4、开中断
		这一步后将响应中断事件，并能跳转中断函数。

##### 2.2、获取用户编译日期信息，获取芯片ID
![[Pasted image 20231206090023.png]]

		获取到的日期会存进内存中的参数表里。

##### 2.3、while(1)
		死循环中，处理一些CANopen的数据，暂时看不明白。


---

#### 3、中断

|序号|中断名|抢占优先级|响应优先级|
|:---:|:---:|:---:|:---:|
|1|TIM3|0|0|
|2|TIM4|1|0|
|3|ExUART(PC)--uart1|0|6|
|4|InUART(DSP)--uart2|0|1|
|5|DMA1|0|2|
|6|InUART(充电板)--uart4|1|1|
|7|DMA2|||
|8|ADC|||
|9|DMA1(通道3)|0|9|

### Tim3中断
		Tim3没有使用更新中断，而是使用比较通道实现多个定时中断：
		vu16 CCR1_Val = 720  * 2;    //100us
		vu16 CCR2_Val = 2160 * 2;    //300us
		vu16 CCR3_Val = 3600 * 2;    //500us
		vu16 CCR4_Val = 7200 * 2;    //1000us
		启用CCR1-4的比较中断。在不同的时间单元下处理不同的任务，例如：
		1、100us
		旋变角存盘，操作数码管。
		设置下一个CCR1_Val，确保100us后再次进入中断
		2、
		高速任务
		设置下一个CCR2_Val，确保下一个300us后进入中断
		3、
		低速任务、PLC_CLK边沿检测处理
		设置下一个CCR3_Val，确保下一个500us能进入中断
		4、
		CAN通讯的某些处理（看不懂，不理解）

### Tim4中断
		该定时器使用更新中断，设定定时时间为1ms,负责调度用户任务，PLC TON TOF后台处理，执行用户任务。

### DMA通道分配
|DMA|通道|用途|
|:---:|:---:|:---:|
|DMA1_Stream5|DMA_Chanel_4|DSP串口通讯，串口2接收|
|DMA1_Stream2|DMA_Chanel_4|串口4接收|
|DMA1_Stream4|DMA_CHenel_4|串口4发送|

### 串口分配
		stm32f405有相当丰富的串口资源，共6个USART，6个串口并没有全部使用。
		
|UART|波特率|用途|
|:--:|:---:|:---:|
|USART1|19200|用于485通讯，与PC通讯|
|USART2|1565217|用于内部与DSP通讯|
|UART4|19200|用于和充电板ARM通讯|


### 收发中断

