>shell的运算符特殊，+-*/效果不同

---
		
		如果像C语言里直接+ - * /，输出的东西会很奇怪

```shell
#!/bin/bash
num=2
num=2+2 #是不是num=4呢？
echo $num
num=2-1 #num会是1吗？.
echo $num
num=2*3 #num会是6吗？
echo $num
num=2/2 #num会是1吗？
echo $num

num=2
num+=1;
echo $num
num-=1;
echo $num
num*=1;
echo $num
num/=1
echo $num
```

![[Pasted image 20230302115429.png]]

		很显然，shell把这些C语言里的赋值运算，都认为是字符串了
		甚至，+=是拼接字符串
		而没有 -= *=  /=的命令。
		哪要如何在shell脚本中进行数学运算呢？

---
### expr
		原生的bash不支持数学运算，但他可以通过其他命令来实现，expr最常用。ezpr是一款表达式计算工具，使用它就能完成表达式的求值操作。

```shell
#!/bin/bash

#expr做简单的加法运算，用法为：

val=`expr 2 + 2`
echo "两数之和为：${val}"
```

>使用expr，需要注意：
>- expr表达式，需要被 `` 所包裹；
>- expr关键字，运算符，数字之间都要用空格相隔

##### 常用的算术运算符
假定已有a=10,b=20;

|运算符|说明|举例|
|:---:|:---:|:---:|
|+|加法|`expr $a + $b`|
|-|减法|`expr $a - $b`|
| * |乘法|`expr $a \* $b`|
|/|除法|`expr $a / $b`|
|%|取余|`expr $a % $b`|
|=|赋值|a=$b|
|== |全等判断，相同返回ture|[ $a == $b ]返回false|
|!=|不等判断，相异返回ture|[ $a != $b ]返回ture|

_条件表达式，一定要在【】之间，并且，要有空格_


实例如下：
```shell
#!/bin/bash

a=10
b=20

echo `expr $a + $b`
echo `expr $a - $b`
echo `expr $a \* $b`
echo `expr $a / $b`
echo `expr $a % $b`

a=100

echo $a

if [ $a == $b ]
then
    echo "a==b"
fi

if [ $a != $b ]
then
    echo "a!=b"
fi
```

---
### 关系运算符

		除去算数运算符外还有关系运算符，但关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

|运算符|说明|举例|
|:---:|:---:|:---:|
|-eq|是否相等|[ $a -eq $b ]|
|-ne|是否不等|[ $a -ne $b ]|
| -gt |左数是否大于右数(>)|[ $a -gt $b ]|
|-lt|右数是否大于左数(<)|[ $a -lt $b ]|
|-ge|左数是否大于等于右数(>=)|[ $a -ge $b ]|
|-le|右数是否大于等于左数(<=)|[ $a -le $b ]|
```shell
#!/bin/bash
#!/bin/bash

a=10
b=20

if [ $a -eq $b ]
    then
        echo "a==b"
    else 
        echo "a!=b"
fi

if [ $a -ne $b ]
    then
        echo "a!=b"
    else 
        echo "a==b"
fi

if [ $a -gt $b ]
    then
        echo "a>b"
    else 
        echo "a!>b"
fi

if [ $a -lt $b ]
    then
        echo "a<b"
    else 
        echo "a!<b"
fi

if [ $a -ge $b ]
    then
        echo "a>=b"
    else 
        echo "a<b"
fi


if [ $a -le $b ]
    then
        echo "a<=b"
    else 
        echo "a>b"
fi
```


---
### 逻辑运算符

|运算符|说明|举例|
|:---:|:---:|:---:|
|&&|逻辑的AND|[ $a -eq 10 && $b -eq 20 ]|
| \|\| |逻辑的OR|[ $a -ne 10 \|\| $b -eq 20 ]|

---
### 字符串
假设a="abc";b="bcd"

|运算符|说明|举例|
|:---:|:---:|:---:|
|=|是否相等|[ $a = $b ]|
|!=|是否不等|[ $a != $b ]|
|-z|检测字符串长度是否为0|[ -z $a ]|
|-n|检测字符串长度是否不为0|[ -n "$a" ]|
|$|检测字符串是否为空|[ $a ]|

_"="在面对字符串的时候，一定是在【】中才是全等判断，否则就是赋值_

---
## 文件检测操作符
>shell脚本的文件运算符可以检测Unix下文件的各种属性，用法比C语言的stat访问文件属性简单得多.

|操作符|说明|举例|
|:---:|:---:|:---:|
|-b file|检测文件是否是块设备文件，如果是，则返回true|[ -b $file ]|
|-c file|检测文件是否是字符设备文件，如果是，则返回true|[ -c $file ]|
|-d file|检测文件是否是目录，如果是，则返回true|[ -d $file ]|
|-f file|检测文件手否是普通文件(既不是设备文件，也不是目录)，如果是，则返回true|[ -f $file ]|
|-g file|检测文件是否设置了SGID位，如果是，则返回true|[ -g $file ]|
|-k file|检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回true|[ -k $file ]|
|-p file|检测文件是否是有名管道，如果是，则返回true|[ -p $file ]|
|-u file|检测文件是否设置了SUID，如果是，则返回true|[ -u $file ]|
|-r file|检测文件是否可读，如果是，则返回true|[ -r $file ]|
|-w file|检测文件是否可写，如果是，则返回true|[ -w $file ]|
|-x file|检测文件是否可执行，如果是，则返回true|[ -x $file ]|
|-s file|检测文件是否为空(文件大小是否是0)，如果是，则返回true|[ -s $file ]|
|-e file|检测文件(目录)是否存在，如果是，则返回true|[ -e $file ]|






