>bxCAN称为_基本扩展协议CAN_。
>1、旨在使用最少的CPU负载管理大量的传入信息
>2、可配置优先级
>3、CAN控制器提供必要的硬件功能，提供CAN时间通信触发方案

##### 特点
>1、发送方面：
>- 三个发送邮箱
>- 可配置的发送优先级
>- SOF发送时间戳
>2、接收方面
>- 两个具有三级深度的接收FIFO
>- 可调整的筛选器组
>- 标识符列表功能
>- 可配置的FIFO上溢
>- SOF接收时间戳
>3、时间触发通信方案
>- 禁止自动重发模式
>- 16为自由运行定时器
>- 最后两个数据字节发送时间戳
>4、双CAN
>- CAN1：管理bxCAn和SRAM的通信
>- CAN2：无法直接访问SRAM，但可以通过CAN1共享

---

		单看上述定义，无法理解bxCAN。
		如今的CAN应用，节点数量不断增多，经常需要通过网关将数个网络连接在一起，，系统的数据量显著增加，同时还加入了和网络管理，诊断信息等消息。
		1、数据类型增加，需要一个增强的筛选机制进行处理；
		2、任务处理信息需要的时间显著增加，数据接收和发送都需要一个合理的缓冲；
		首先，bxCAN对数据的接收和处理，完全交给硬件，而用户需要对STM32进行配置的，为以下几项：
		1、CAN通信频率
		2、请求发送
		3、处理接收
		4、管理中断
		5、获取诊断信息

---
### 1、位速率
在[[CAN_bus]]中没有提到的，关于发送单元是如何发送，如何采样的问题。

		在发送单元非同步的情况下，发送单元每秒钟发送的位数称为位速率。
		首先，位速率和波特率是不同的，波特率的bound等于1个bit时，两者才是相同的应以，如果bound等于2bit，则波特率是为速率的两倍。

>bound是符号的指代，所谓一个bound等于一个bit，是说这个符号只有两种指代，一个0，一个1，如果是一个符号有多个指代，则bound_rate和bit_rate是完全不对等的。（例如这个符号指是、不是、可能是三种情况）


#### CAN的位时间

		CAN每发送一个显性位，或者隐性位，消耗一个位时间。一个位时间，可以分为4段。每个段有多个最小时间单位(位时序)Tq组成。

|段名称|段作用|Tq数量|
|:---:|:---:|:---:|
|同步段SS|总线单元通过此段实现时序同步，也就是每个位都有同步点|1Tq|
|传播时间段PTS|用于延迟时间补偿，时间长短是随机的|1-8Tq|
|相位缓冲段PBS1|各个单元以自身的时钟工作，细微的时差会累计，这一段用户吸收此误差|1-8Tq|
|相位缓冲段PBS2|同上|2-8Tq|
|再同步补偿宽度SJW|同步再补偿|1-4Tq|

_CAN收发器采样就在缓冲段1和缓冲段2之间，由于个位时间中，PTS和PBS1的时间不定，因此采样点也不是固定某一时刻的。_

_同步段SS，电平从1到0时，是有一定的坡度的，这个坡度时间，最好控制在SS段，否则PTS和PBS1势必要延长。_

### 2、注意事项
首先看STM32的双CAN框图：
![[Pasted image 20230828120946.png]]

		可见，两个CAN都有各自的配置寄存器(最左边)，FIFO数据缓冲区，发送邮箱。每个FIFO，发送邮箱均有3个邮箱，每个邮箱可以存储一个帧数据。
		但是两个CAN公用28个验收缓冲器。
		这个缓冲器，谁用多少个，可以在CAN_FMR寄存器进行分配。

![[Pasted image 20230828121341.png]]

		这28个验收缓冲器，也叫过滤器，也叫筛选器。它的存在就是代替CPU对数据的验收。可以理解为硬件自动判断总线上读到的数据是否正确。
		28个缓冲器，每个又由2个寄存器CAN_FxR1和CAN_FxR2组成。
		可以理解为，收到数据的仲裁段就在这里判断。用户在其中一个寄存器，存入我们需要关心的数据帧的ID，另一个寄存器将是收到的ID。
		这两个寄存器可以通过分配位空间，达到认识多个ID的状态。

![[Pasted image 20230828122540.png]]


---
### CAN的通信频率

一个bit的所需要的时间可以简化位 同步位时间+传播缓冲时间+相位缓冲时间。
在STM32中，将一个bit的时间分为 同步段时间+位段BS1+位段BS2时间。

![[Pasted image 20230828143536.png]]

		两个位段时间由寄存器配置，同步段时间默认为1Tq，而Tq时间也有寄存器配置，通过对总线时钟进行分频得到。

### CAN配置ID

		CAN通讯节点，只对带有指定的ID信息感兴趣。ID存在于仲裁段中。
		其中：
		1、标准格式的ID为11位
		2、拓展格式的ID为29位

_在STM32中，过滤ID的工作有过滤器组完成_

##### 工作模式
		过滤器组的工作模式分为两种，一种是掩码模式，另一种是标识符列表模式
		0、stm3f4共有28个过滤器组，分配给CAN1和CAN2,如果CAN2不做特别配置，CAN2是默认无法使用任何过滤器组的;
		
		1、过滤器的组成
		每个过滤器组均由2个32位寄存器组成，一个标识符寄存器，一个屏蔽(掩码寄存器。
		
		2、过滤器组工作模式分为，掩码模式(MASK_MODE)和标识符模式。
		掩码模式下，用户主要使用掩码寄存器，去配置某些指定的位，只要收到的ID符合掩码，数据就会被保留。此时的标识符寄存器中，也应该是一个符合掩码的ID。
		标识符模式下，掩码寄存器也将被当作标识符寄存器使用，只有当ID满足其中一个标识符寄存器的数值。（每一位都一一对应），数据才会被保留。
		
		3、过滤器组的位宽
		每个CAN通道可以使用不止一个过滤器组，每个过滤器组的位宽都可以独立设置。根据设置的位宽，会影响到两个过滤寄存器的过滤ID。
		 ●1个32位过滤器，包括：STDID[10:0]、EXTID[17:0]、IDE和RTR位  
        ●2个16位过滤器，包括：STDID[10:0]、IDE、RTR和EXTID[17:15]位
		设置过滤器组32位，寄存器也全都是32位，ID判断时，能够判断拓展ID,而设置16位时，虽然能够判断多一个ID但只能是标准格式。

过滤器配置如下图：
![[Pasted image 20230828170132.jpg]]

库函数中，看似结构体直接填写ID的高位地位，既可以达成滤波，实际不然：
看源代码：
![[Pasted image 20230828171431.png]]

		CAN过滤器初始化，仅仅是把传入的高位地位填充进寄存器无法得到正确的滤波结果，因此，在设置高位地位时，要注意到IDE位，RTR位和最低位一个0的位置。否则滤波不正确，接收不到想要的数据。
		一般来说，将拓展格式的ID存放在标识符寄存器，而将标准格式的ID存放在掩码寄存器。但掩码寄存器的ID放置要更加复杂些。
		掩码寄存器存储的应该是期望ID的掩码，也就是ID取反。因此，在获得掩码后，需要及时取反，并不要忘记填充IDE,RTR和第0位。