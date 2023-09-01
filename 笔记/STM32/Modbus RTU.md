>1、标准、开放、免费
>2、支持多种电气接口，如232，485
>3、帧格式简单
>4、Modbus RTU常见用于RS485的物理层
>5、它是应用层的协议
>6、大端格式，高位先出
### 1、Modbus 概述
		Modbus协议是应用于电子控制器上的一种通用语言。通过此协议，控制器相互之间、控制器经由网络(例如以太网)和设备之间可以通信。它已经成为一通用工业标准。有了它，不同厂商生产的控制设备可以连成工业网络，进行集中监控。
		此协议定义了一个控制器能认识使用的消息结构，描述了一控制器请求访问其它设备的过程，如何回应来自其它设备的请求，以及怎样侦测错误并记录。它制定了消息域格局和内容的公共格式。
		Modbus三兄弟为：Modbus_RTU，Modbus_ASCII，Modbus_TCP。如果时控制类行业，三者之间的关系为：

![[Pasted image 20230821095730.png]]

### 2、链路层
		RS485作为物理层，不必多说。在链路层，Modbus的表现形式为主从模式。以485总线来看，32个最大总线设备中，将有一个设备被Modbus规定为主机，其余设备均为从机。主机将数据一对一的发送到从机上，称为单播；主机将数据发送给总线上的所有从机，称为广播。
		单播状态下，报文中必定包含一个从机地址，从机接收到数据需要进行应答，而广播状态下，报文中包含广播地址，从机无需对广播信息进行应答。Modbus的广播地址为0。
		Modbus—RTU对从机地址进行了规定，如下：

![[v2-5dff464e0104240560de9a25ab4949aa_720w.webp]]

		由图可知，0默认为广播地址，1-247为可使用的从机地址，但485最多支持32个设备，因此会有大量的空余地址。248-255为预留地址。根据数据即可知，Modbus-RTU的数据格式中，必然有一个byte的大小的地址。
_**链路层最为重要的功能，是对通讯介质的管理**_

##### 2.1、主机状态管理（主机视角下：总线状态）
		主机状态管理，可以理解为，主机的动作流程图，什么时候，主机占用总线，什么时候，主机释放总线：

![[v2-9e16c8913cd71a4ee2ce47fb4818fa49_720w.webp]]

		整个流程为，主机系统开机，总线会进入空闲状态。
		1、主机发送广播，总线进入广播延时状态，主机发送完毕，推出广播延时，回到空闲
		2、主机单播，总线进入等待应答状态，收到应答进行应答处理状态。否则进入错误处理
		3、应答处理完毕，总线回到空闲状态，否则进入错误处理

##### 2.2、从机状态管理
![[v2-5c94e4768edcd1c4596915a297d69de2_720w.webp]]

		从机流程面对单播时，需要准备应答的报文。
		1、系统开机，总线空闲
		2、收到广播，做CRC校验，校验通过则处理主机信息
		3、收到单播，做CRC校验，校验通过，处理主机信息，回复主机正确报文
		4、校验错误，回复错误报文
##### 2.3、介质管理
		介质管理，主要分为，数据定长，字节时间间隔。也就是，总线需要有一定的空闲时间。
		1、两帧数据之间最少间隔3.5个字节时间，否则认为是一帧连续数据
		2、两个字节之间，数据发送速率不得低于1.5个字节时间。否则认为是帧字节错误
		字节时间由设定的通信波特率决定。

![[v2-a0df7ea7b0e42aef4b91baccc55fd6b6_720w.webp]]
![[v2-4e8cc419bfeba5a3baf6eb16ab498d5c_720w.webp]]

### 3、数据帧结构
![[v2-fb3de6ba1091bb428c892473083ad06a_720w.webp]]

		Modbus一帧数据，包含地址信息，功能码信息，数据，和16位CRC校验。其中功能码对应报文命令，这个命令在应用层会单独规定，解析数据时，必然对功能码进行查表匹配。
		这一帧数据，在Modbus中，被抽象为4个部分

|描述|大小|属性|功能|
|:---:|:---:|:---:|:---:|:---:|
|离散输入|1bit|只读|系统的输入输出|
|线圈|1bit|可读可写|由应用程序定义|
|输入寄存器|16bit|只读|数据用于系统的输入输出|
|保持寄存器|16bit|可读可写|2个字节由程序定义|

_从这个表看，非常的难以理解，Modbus本身是面对工控的PLC应用，不必费力理解上面这个表。_


#### 利用软件体检Modbus_RTU，实际数据收发如下(功能码0x03)

###### 1、利用Modbus poll 和 Modbus slave模拟一个Modbus_RTU的收发过程：
打开两个软件如下：
![[Pasted image 20230821150405.png]]

###### 2、Modbus slave是配置从机的软件，设置从机的地址为0x01，设置方法：setup->slave defination。
主要配置参数为 Slave ID，这是从机的通讯地址。Address为寄存器地址，Quantity为寄存器数量。

当前配置为：从机地址0x01，读写起始地址为0x0000，寄存器数量为10个，则最高可访问到地址0x00A0。
![[Pasted image 20230821150638.png]]

###### 3、设置主机，setup->read/write defination 。主机需要设置意图访问的从机地址，期望读写的寄存器地址，读/写寄存器的数量。

![[Pasted image 20230821151708.png]]
红框内表示主机将会发送的数据帧。

>_poll和slave的Address为10进制输入_
>![[Pasted image 20230821151948.png]]
>Modbus的Address理解上比较特别，每种数据模型有65535个元素。是专为PLC所定制的
>每个地址从1开始，但用不到65535，因此一般定义到10000以内。
>- 线圈地址：1~09999
>- 离散量输入地址：20001~19999
>- 输入寄存器地址：30001~29999
>- 保持寄存器地址：40001~39999
>_在单片机中，直接考虑线圈地址即可_



###### 4、设定好地址起始为0，读取6个寄存器，则收发情况，可以在Communication traffic中看到

![[Pasted image 20230821150126.png]]

发送的数据帧如下：

|地址|功能码|读写地址|寄存器个数|CRC校验码|
|:---:|:---:|:---:|:---:|:---:|
|01|03|0000|0006|C5C8|
返回的数据：

|地址|功能码|读到数据个数|实际数据|CRC校验码|
|:---:|:---:|:---:|:---:|:---:|
|01|03|0C|XXXX|3C5D|
_每个数据均是16位的。读到的数据个数是按8个位一个数据计算，因此读取6个寄存器返回0x0C，即12个数据。_


### 4、常用的3个功能码和数据的形态
>不同的功能码，约定不同的功能
#0x03  ：读取寄存器数据
#0x06  ：写入单个寄存器
#0x10  ：写入多个寄存器

0x03功能码下，主从数据格式如下：
![[Pasted image 20230821155828.png]]

0x06功能码下，主从数据如下：
![[Pasted image 20230821155854.png]]

0x10功能码下，主从数据如下：
![[Pasted image 20230821155914.png]]

---
## 5、STM32F4实现Modbus RTU

		优先配置好串口通信，Modbus可以理解为应用层的软件规定。针对串口收到的数据，进行帧结构分析即可。
		为了代码的编程便利性提高，做宏定义准备：
```C
#define MODBUS_READ 0x03
#define MODBUS_SINGLE_WRITE 0x06
#define MODBUS_MULTIPLE_WRITE 0x10

#define MODBUS_ADRESS 0x01
#define MODBUS_MAX_ADRESS 0x5555

#define ADRESS_ERROR -1
#define FUNCTION_ERROR -2
#define LENTH_ERROR -3
#define CRC_ERROR -4

```

		1、前三个宏定义，设置不同的功能码。程序应能分辨不同的功能码，并针对不同的功能码调用不同的处理函数。
		2、中间两个宏定义为地址码。前者为自身的Modbus通讯地址，规定为0x01,数据帧中，非此地址码的数据接收后报错。后者为读写地址最大范围，此数值依据实际安排的内存范围修改，务必谨慎更改，避免内存访问越界。
		3、后四个宏定义为出错码，当程序监测到数据帧中某部分的数值不符合预期，将回复错误码。


---
		
		串口收到一帧完整的数据，将依据Modbus_RTU的特点对数据进行逐个字节的分析，代码如下：
```C
int modbus_check(u8* rx_data)
{
    //检验地址是否正确
    if(rx_data[0]!=MODBUS_ADRESS)
    {
        return ADRESS_ERROR;
    }
    //功能码是否正确
    if((rx_data[1]!=MODBUS_READ)&&(rx_data[1]!=MODBUS_MULTIPLE_WRITE)&&(rx_data[1]!=MODBUS_SINGLE_WRITE))
    {
        return FUNCTION_ERROR;
    }

    //访问地址是否超出范围
    if(detach_modbus_adress(rx_data)>=MODBUS_MAX_ADRESS)
    {
        return LENTH_ERROR;
    }
    //CRC校验
    if(modbus_crc_check(rx_data,(MY_UART1_STA&0x7FFF))!=1)
    {
        return CRC_ERROR;
    }
    return 1;
}
```

		程序流程为：
		1、先判断接收的数据帧的地址
		2、判断功能码
		3、确认地址是否超限
		4、确认CRC校验
		如果一帧数据能够经过上述校验，认为是收到一帧正确的数据，则通过校验。

其中CRC校验采用Modbus_CRC16，其对应的多项式为0x8005，该CRC校验源码如下：
```C
/*
    Modbus CRC 校验与星辰上位机的校验方式不同
    步骤为：
    初始设定CRC为 0xFFFF
    1、将CRC与要校验的数据的第一个字节进行异或，结果存储再CRC中
    2、将CRC右移一位，记录被右移掉的那一位。如果是0，则继续右移，如果是1，则将CRC与CRC16余子式0xA001进行异或
    3、重复步骤2，一共8次
    4、重复上述步骤，直到所有的字节均被处理
    5、将CRC结果的高八位和低八位进行调换，得到结果

    星辰上位机的CRC校验是CCITT算法
    和Modbus_CRC校验的算法有差异

*/
//Modbus协议的CRC16校验
u16 Modbus_RTU_CRC16(u8* rx_data,int len)
{
    u8 i,j;
    u16 CRC16 = 0xFFFF;
    u8 temp = 0;
    for(i = 0;i<len;i++)
    {
        CRC16^=rx_data[i];
        for(j= 0;j<8;j++)
        {
            if((CRC16&0x0001)==0x0000)
            {
                CRC16>>=1;
                continue;
            }
            else
            {
                CRC16>>=1;
                CRC16^=0xA001;
            }  
        }
    }

    temp = CRC16>>8;
    CRC16 = CRC16<<8|temp;
    return CRC16;
}

//之后，将接收数据的CRC校验码分离，取前方的数据传入校验函数，比较校验结果
u8 modbus_crc_check(u8* rx_data,u8 len)
{
    u16 crc_check = 0;
    crc_check|=rx_data[len-2];
    crc_check<<=8;
    crc_check|=rx_data[len-1];

    if(crc_check!=Modbus_RTU_CRC16(rx_data,len-2))
    {
        return 0;
    }
    return 1;

}

```


		通过CRC校验后，依据不同的功能码，调用不同的处理方法：

![[Pasted image 20230831093556.png]]

		怎对不同的功能码，实现不同的功能函数，在这些功能函数中，既要完成数据的处理，也要完成回复数据的封装。同时，需要注意Modbus数据的发送是大端模式，数据的高低位和小端模式的ARM是颠倒的，收到数据后，应当将数据高低位置换后，在送出，或写入。具体代码实现如下：

```C
//读取功能(测试把crc表读出去)
u16 register_data_buf[10];

void Modbus_master_read(u8 *rx_data)
{
    u8 i = 0;
    u16 initial_adress = 0;
    u16 memory_size = 0;
    u16 temp;
    u16 CRC16;
    TX_DATA_LEN+=1;//除去1byte地址，1byte功能码,2byteCRC校验码，还有1byte回复数据量

    initial_adress = (((u16)rx_data[2])<<8)|rx_data[3];//获取读取的初始地址
    memory_size = (((u16)rx_data[4])<<8)|rx_data[5];//获取读取的数据个数

    TX_DATA_LEN+=memory_size*2;
    for(i=0;i<memory_size;i++)
    {
        if(i>255)
        {
            break;
        }
        register_data_buf[i] = crc_ta[initial_adress+i];  //如果读取的数据的地址更换，要修改这个区域
    }
    //i是实际上读到的数据个数
    MY_UART1_TX_BUF[0] = MY_UART1_RX_BUF[0];
    MY_UART1_TX_BUF[1] = MY_UART1_RX_BUF[1];
    MY_UART1_TX_BUF[2] = i*2;
    //填充数据
    for(i=0;i<MY_UART1_TX_BUF[2];i++)
    {
        temp = register_data_buf[(i/2)];
        MY_UART1_TX_BUF[3+i] = (u8)(temp>>8);
        MY_UART1_TX_BUF[3+(++i)] = (u8)temp;
    }
    //填充CRC校验位
    CRC16 = Modbus_RTU_CRC16(MY_UART1_TX_BUF,TX_DATA_LEN-2);
    MY_UART1_TX_BUF[TX_DATA_LEN-2] = (u8)(CRC16>>8);
    MY_UART1_TX_BUF[TX_DATA_LEN-1] = (u8)CRC16;
}

//写入功能
void Modbus_single_write(u8* rx_data)
{
    u16 write_adress = (((u16)rx_data[2])<<8)|rx_data[3];   //获取写入的首地址
    u16 data = (((u16)rx_data[4])<<8)|rx_data[5];           //写入的数据
    TX_DATA_LEN = MY_UART1_STA&0x7fff;
    //写入地址
    register_data_buf[write_adress] = data;
    copy_data2TX_BUF(rx_data,TX_DATA_LEN);//数据原封不动的传出去

}

//批量写入
void Modbus_multiple_write(u8* rx_data)
{
    int i;
    u8 *src_ptr;
    u8 *target_ptr;
    u16 CRC16,temp;
    u16 initial_adress = (((u16)rx_data[2])<<8)|rx_data[3]; //获取起始地址
    u16 register_num = (((u16)rx_data[4])<<8)|rx_data[5]; //获取寄存器个数
    u16 data_num = rx_data[6];//获取写入字节数
    //获取写入数据
    //u8 字节空间写入
    src_ptr = ((u8*)rx_data+7);
    target_ptr = (u8 *)(register_data_buf+initial_adress);
    for(i=0;i<data_num;i++)
    {
        *(target_ptr+i) = *(src_ptr+i);//写入颠倒
    }

    //将数据重新翻转
    for(i=0;i<register_num;i++)
    {
        temp = register_data_buf[i]>>8;
        register_data_buf[i] = (register_data_buf[i]<<8)|temp;
    }

    //封装TX_BUF
    MY_UART1_TX_BUF[0] = MY_UART1_RX_BUF[0];        //封装地址
    MY_UART1_TX_BUF[1] = MY_UART1_RX_BUF[1];        //封装功能码
    src_ptr = (u8 *)rx_data+2;
    target_ptr = &MY_UART1_TX_BUF[2];


    for(i=0;i<4;i++)
    {
        *(target_ptr+i) = *(src_ptr+i);  
    }

    //补上CRC校验位
    TX_DATA_LEN = 8;
    CRC16 = Modbus_RTU_CRC16(MY_UART1_TX_BUF,TX_DATA_LEN-2);
    MY_UART1_TX_BUF[TX_DATA_LEN-2] = (u8)(CRC16>>8);
    MY_UART1_TX_BUF[TX_DATA_LEN-1] = (u8)CRC16;
}
```