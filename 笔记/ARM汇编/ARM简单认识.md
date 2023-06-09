>一块芯片，其CPU，RAM，ROM各司其职。
>当我们编写好程序之后，烧写程序到单片机，实际上是烧入ROM中，该部分即使掉电也会保存。当我们启动单片机，ROM中的程序就会移入RAM。然而CPU是无法处理RAM中的数据的，只能识别指令，因此要先将ARM的数据送入寄存器，由寄存器操作，再将操作结果送回RAM中，如此往复，直到程序处理结束。

---
### Load/Store结构
	引文所描述的就是Load/Store结构，这样的设计，处理速度十分依赖于CPU,RAM,ROM之间的数据总线的大小。并且，寄存器的数量也至关重要。

---
### ARM体系下的CPU的7种工作模式
#用户模式     #外部中断模式   #管理模式    #数据访问中止模式
#快速中断模式     #外定义指令中止模式      #系统模式

>- 管理模式（SVC）
>    CPU上电后来到的第一个模式；
>- 用户模式
>用户程序的工作模式，也就是用户态；
>- 系统模式
> 特权模式，也就是内核态；
>- 一般中断模式
> 特权模式，用来处理硬件中断；
>- 快速中断模式
>用来处理紧急的中断请求，用在高速数据传输和通道中
>- 数据访问中止
>  当用户访问非法地址会进入此模式，他抛出段错误指令
>- 外定义中止
>硬件协理软件仿真时会进入，类似ST-Link时的状态，又或者CPU遇到不认识的指令

_除了用户模式外，其他6个模式都是特权模式。_
_特权是指，将状态寄存器的内容放到通用寄存器，或者将通用寄存器内容放入状态寄存器。两者结合可以达到修改状态寄存器的目的。_

**快速中断模式的中断，不会被其他中断所打断，优先级更高也不行**


		通过这7种模式，回看Linux系统的启动过程。
		首先是上电，所有硬件上电后状态无误，存储在最底层的BSP板级支持包最先启动。即CPU进入SVC，PC寄存器指向0x0000 0000 地址。BSP里的bootloader程序，会引导Linux内核的加载。
		因为BSP存储的位置为0x0000 0000的NOR FLASH或者SRAM,因此当PC寄存器一指向此处，BSP就启动。它引导硬件初始化，以及Linux内核。此时的Linux内核，也运行再SVC模式下。当内核启动完毕，始祖进程init就开始运行。由于此时ARM还在SVC模式下，init进程可以操作状态寄存器，他将CPSR寄存器的M[4:0]位设置为10000。
		当该寄存器被设定，整个ARM就会进入用户模式，往后的所有程序(除中断外)，将运行在用户态下，从而实现操作系统内核的保护。想要访问到内核资源，用户态的程序只能切换到内核态。
		值得注意的是，Linux的内核态不仅仅是管理模式，其他6个状态都是内核态，关键点就在于，他们访问资源是否受限，不受限都是内核态。
		同理，Linux的用户态，也不局限于ARM的用户模式。用户态的程序，只是从用户模式下启动而已。

---
### ARM的两种工作状态
>这个工作状态是芯片级的，并非操作系统的内核态和用户态。而是ARM状态和THumb状态

		这两种状态时CPU所处的状态，区别是处理器能够识别什么样的指令
		ARM状态下，CPU的所有指令都是32位指令
		thumb状态下，则是16位指令
		此外ARM7版本之后推出了thumb-2指令，它兼容16位和32位指令，实现了更高的性能，更有效的功耗以及更少的内存占用。青出于蓝而胜于蓝。
		此外还有一个非工作状态，调试状态，处理器停机时，处在调试状态

_改变ARM的工作状态，则由状态寄存器CPSR的某些标志位改变_


ARM处理器复位后，执行代码总是处于ARM状态。
Cortex-M3只有Thumb-2和调试状态。

---
### ARM工作模式切换
>ARM工作模式得切换离不开状态寄存器SR。
> 
>	ARM的状态寄存器分为 SPSR 以及CPSR。CPSR能够设置相当多的标志位，使得ARM工作在各种状态，而SPSR则是负责保存前一个工作模式的CPSR，这样，当返回前一个工作模式时，SPSR就能将CPSR的数值恢复。

工作模式的切换需要硬件和软件相互结合
以下为CPU的动作：
1、R14保存前一工作模式的下一条地址
2、SPSR复制CPSR
3、CPSR切换要工作的模式信息
4、PC跳转对应模式的地址
以下为软件的动作(复现前一个工作模式)
5、R14减去一个适当的值赋值给PC
6、将SPSR的值回写CPSR

---

### 寄存器组
##### 通用寄存器R0-R12
寄存器往往用来协助CPU处理数据，最为经典的就是子程序之间的切换。

		1、R0-R3
		负责传入函数参数，传出函数的返回值。而在子函数调用之间，R0-R3可以用作其他任何用途。函数调用结束，该函数必须恢复调用前的原样
		2、R4-R11
		存放函数的局部变量，如果函数使用了这些寄存器，函数结束前，要通过栈回复寄存器调用前的原样
		3、R12
		是内部调用的暂时寄存器ip,两个函数间无缝接合的胶合代码需要通过它实现。函数调用结束后，该寄存器不必恢复   
		4、R13 
		栈，不能被随意使用
		5、R14
		R12时胶合，则R14是链接。
		6、R15
		程序计数器PC
		3级流水线：取指、译码、执行


---
### ARM的寻址方式
>数据传输的方式有 内存->寄存器、内存->内存、寄存器->寄存器。以此衍生出多种寻址方式。

#寄存器寻址   #立即寻址   #寄存器偏移寻址   #寄存器间接寻址
#基址寻址   #多寄存器寻址   #相对寻址

_51单片机也是这7种_

= 寄存器寻址
MOV R1,R2;

=立即数寻址
MOV R1,# 0xFF;

=间接寻址
LDR R1, [ R2 ]

=基址寻址(间接寻址的基础上添加偏移量)
LDR R1,[ FP,#2 ]

=多寄存器寻址(操作多个寄存器 例如R14 R2 R1 R0)
LDMIA  SP!,{R0-R2,R14}；

=相对寻址(是基址寻址的特殊情况，是以PC指针为基址进行偏移的)
B Loop 的跳转

= 寄存器偏移寻址
间接寻址的基础上，对操作数2进行左移右移，得到新的操作数







