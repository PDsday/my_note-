>1、CPU通过I-Code 或 D-Code总线访问Flash。
>2、I-Code通过AHB32位指令总线从Flash中取指令
>3、D-Code通过AHB32位数据总线从Flash中去数据
>4、Flash总线有128位宽
>5、STM32F40xFlash容量1MB，F42/3容量高达2MB
>6、Flash支持字节、半字、字和双字写入
>7、擦除的最低单位微为一个扇区，最大单位为全擦除
>8、F40X的Flash有4个16KB扇区，1个64KB扇区，7个128KB扇区，共1MB
>9、低功耗模式下不掉电（PWR）
>10、Flash中含有30KB的系统存储区域，512字节的OTP区域和16个字节的选项字节


### 1、地址定义
		Flash地址，已扇区为单位。在库函数中，定义可扇区的编号，但没有给扇区适配合适的地址。这是因为库函数面向不同型号的STM32，不能具体的为单个芯片定义它的Flash扇区。因此，需要手动的设置扇区的地址，然后将地址与库函数的扇区编号进行匹配。
		库函数中，扇区的编号定义如下：

![[Pasted image 20230823092442.png]]

		F405只有12个扇区。用不到扇区Flash_Sector_12及以后的扇区。扇区的地址需要手动描述。可以查阅相关数据手册，进行定义。手册所给出的地址，就是实际的Flash地址。F405不存在MMU单元，也就没有虚拟地址，所有地址均是实际物理地址。
		根据参考手册，定义地址如下：

![[Pasted image 20230823095649.png]]

		通过函数，将物理地址，转换成扇区编号。
```C
u16 Get_Sector_number(u32 adress)
{
    if((adress>=Adress_Sector_0)&&(adress<Adress_Sector_1))
    {
        return FLASH_Sector_0;
    }
    else if((adress>=Adress_Sector_1)&&(adress<Adress_Sector_2))
    {
        return FLASH_Sector_1;
    }
    else if((adress>=Adress_Sector_2)&&(adress<Adress_Sector_3))
    {
        return FLASH_Sector_2;
    }
    else if((adress>=Adress_Sector_3)&&(adress<Adress_Sector_4))
    {
        return FLASH_Sector_3;
    }
    else if((adress>=Adress_Sector_4)&&(adress<Adress_Sector_5))
    {
        return FLASH_Sector_4;
    }
    else if((adress>=Adress_Sector_5)&&(adress<Adress_Sector_6))
    {
        return FLASH_Sector_5;
    }
    else if((adress>=Adress_Sector_6)&&(adress<Adress_Sector_7))
    {
        return FLASH_Sector_6;
    }
    else if((adress>=Adress_Sector_7)&&(adress<Adress_Sector_8))
    {
        return FLASH_Sector_7;
    }
    else if((adress>=Adress_Sector_8)&&(adress<Adress_Sector_9))
    {
	    return FLASH_Sector_8;
    }
    else if((adress>=Adress_Sector_9)&&(adress<Adress_Sector_10))
    {
        return FLASH_Sector_9;
    }
    else if((adress>=Adress_Sector_10)&&(adress<Adress_Sector_11))
    {
        return FLASH_Sector_10;
    }
    else
    {
        return FLASH_Sector_11;
    }
}
```


_**如果STM32的启动方式是FLash启动，则程序会存储在Flash中，并从0x0800 0000开始执行，因此如果对扇区进行擦除，要格外注意STM32的启动方式，避免擦除了程序导致不可挽回的后果**_

### 2、FLash读写
		对Flash读取，则直接解引用地址后后就可以获得相应的数据，非常简单。难度主要在Flash的读取上，需要注意一些细枝末节的情况。

###### 2.1、读取接口
		Flash数据读取的准确度，会受到HCIK(SYSCLK)和电源电压的影响。低速的时钟或者较低的电压，会让读取过程变慢。尤其是当电源电压低于2.1V时，需要将缓冲器关闭。因此在对Flash读取前，需要配置正确的等待周期数---LATENCY。

缓冲器是预取缓冲，为了保证FLash的读取速度，CPU会将经常访问的数据放到缓冲区。如I-Code取指令时，CPU取出第一个指令后，第二个指令就已经在这个缓冲区中就绪了。

		Flash对时钟信号的频率也有要求：F405/7器件中，有如下要求
		1、当VOS = 0，HCLKmax = 144MHz;
		2、当VOS = 1，HCLKmax = 168MHz;
		电压的和等待周期息息相关，具体如下表：

![[Pasted image 20230823112203.png]]

		如果系统经过复位之后，CPU的频率只有16MHz，等待周期只有0WS，如果需要读取FLash，就要先更改CPU的频率，ST官方推荐了相关步骤：

![[Pasted image 20230823112804.png]]

![[Pasted image 20230823112817.png]]

###### 2.2 写入接口
		Flash无法对指定地址进行单个写入，它的每个写入过程都是以扇区为单位的，因此，在写入前，就需要将扇区进行擦除。擦除或写入过程中，HCLK的频率不能低于1MHz，同时写入Flash或者擦除FLash的过程中，器件一但发生复位，Flash中的内容将无法预料。
		在写入器件时，任何读取FLash的内容都会导致Flash总线发生堵塞。因此，写入时，要禁止一切读取Flash的操作。
		Flash默认时不允许写操作的，包括复位之后，Flash也会回到一个锁定的状态，必须通过FLASH_CR寄存器进行解锁。解锁方式为，向密钥寄存器，FLASH_KEYR写入密钥。
		KEY1 = 0x45670123
		KEY2 = 0xCDEF89AB
		两个密钥在库函数中均有预先定义。
		
![[Pasted image 20230823114412.png]]

		Flash的写入的过程是按指定的位数进行并行写入。这个位数是可以配置的，但它和电源电压息息相关，需要按表配置Flahs_CR的PSIZE位。如果配置和电压之间不一致，写入或者擦除操作会出现意外的结果，即使后续的读指令认为已经完成的了写入或者擦除，也无法确定写入或擦除操作是否真的成功。

![[Pasted image 20230823115023.png]]

		批量擦除只针对12个扇区，对OTP扇区和配置扇区没有影响。
		扇区擦除，ST官方亦给出了步骤：

![[Pasted image 20230823121742.png]]

----

		写入FLash在STM32中认为是Flash编程，因此他的英文为program。库函数中Flash带有此单词的便令均是和写入Flash相关的。
		写入操作，ST同样给出了标准：
		
![[Pasted image 20230823122018.png]]

		在写入的过程中，Flash的特性和一些Flash芯片，如为w25q128是相同的，某个单元从‘1’变为‘0’时，无需进行擦除，可以直接连续写，但从‘0’变为‘1’就需要进行擦除操作。根据这个特点。擦除Flash后，单元的默认值为‘1’是最合适的。
		Flash在任意操作解除，或者触发某些错误的时候，会发生中断，如下表：
		
![[Pasted image 20230823122349.png]]

  _更多细节有待发现。_

### 3、利用库函数编程实现
>综上：写入Flash前需要确认：
>- 禁止中断
>- 解锁Flash
>- 清楚标志位
>- 擦除Flash
>- 等待擦除完毕在写入Flash
>- Flash上锁
>- 开启中断
>读写Flash的过程一般不需要刻意关闭中断，在写这些地方时，中断不会被相应。但开关中断也是一个较为保险的手段，避免玄学错误。

_库函数省略了相当多繁琐的步骤，使得Flash的擦除写入变得简单，相比复杂的理论，实际代码简单太多_

```C
//向Flash写入,有以字写入，半字写入  选择半字写入    FLASH_Status FLASH_ProgramHalfWord(uint32_t Address, uint16_t Data);

void Flash_write(u32 adress,u16 *data,u32 len)
{
    //计算出结束地址
    int i;
    u32 end_adress = len*2+adress;
    flash_status = FLASH_COMPLETE;  
    //关闭中断

    //解锁Flash
    FLASH_Unlock();
    //清楚标志位
    FLASH_ClearFlag(FLASH_FLAG_EOP|FLASH_FLAG_PGAERR|FLASH_FLAG_PGPERR|FLASH_FLAG_PGSERR|FLASH_FLAG_RDERR|FLASH_FLAG_WRPERR);

    //禁止数据缓存
    FLASH_DataCacheCmd(DISABLE);

    //擦除Flash
    //写入是否会跨扇区
    if(Get_Sector_number(adress)!=Get_Sector_number(end_adress))
    
        flash_status = FLASH_EraseSector(Get_Sector_number(end_adress),VoltageRange_3);
        if(flash_status!=FLASH_COMPLETE)//擦除出错
        {
            return ;
        }
    }

    flash_status = FLASH_EraseSector(Get_Sector_number(adress),VoltageRange_3);
    if(flash_status!=FLASH_COMPLETE)//擦除出错
    {
        return ;
    }

    //等待擦除完毕

    //写入Flash
    if(flash_status == FLASH_COMPLETE)
    {
      for ( i = 0; i < len; i++)
      {
        if(FLASH_ProgramHalfWord((adress+i*2),*(data+i))!=FLASH_COMPLETE)
        {
            break;
        }
      }
    }

	FLASH_DataCacheCmd(ENABLE);
    //Flash上锁
    FLASH_Lock();
    //重新开启中断

}
```


		这个函数并不完美，首先是跨扇区的问题，这个只能判断起始和结束两个扇区，如果扇区跨度三个即以上，中间的扇区将被忽略，没有擦除就会导致写入时出现错误。
		然后时没有对写入错误做处理，每次出错都仅仅是退出，没有返回值，外部不知道有没有完成写入。用户传入的地址错误也没有做校验等。

---
>读取Flash的数据没有任何注意事项，直接将地址解引用即可

```c
u16 data_buf[16];
//读取Flash
void Flash_read(u32 adress,u32 len)
{
    u32 i = 0;
    for(i=0;i<len;i++)
    {
        data_buf[i] = *((u32*)(adress+i*2));
    }
}
```



### 4、写入Flash需要注意的问题

		首先是写入的扇区。由于STM32通过BOOT引脚，可以选择程序的启动区域。在一般情况下，BOOT选择FLash启动是非常常见的。这个时候，代码会存储的Flash当中，一旦对FLash进行了擦除，就会让代码丢失，CPU从I-Code和D-Code访问Flash得不到正确的指令和数据，就会陷入死机。
		因此，如果选择了FLash启动，一定注意代码的大小，存储代码需要多少个字节，用掉了那些扇区。进行Flash读写的时候，要注意避开这些扇区。

		使用JLink或者ST-Link，如果没有注意配置，那么代码一般会写入Flash当中。此时一旦BOOT选择冲突，则单片机无法运行，或者读写Flash没有注意擦除的空间，将代码擦掉了，也会影响单片机的运行。





