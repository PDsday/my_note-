		shell脚本有相当多的特殊符号，每个符号使得语句的用法大不相同。

### shell的概念
- shell即使解释型的编程语言，也是这种编程语言的解释器的名字
- 解释器会对shell脚本中的语句按顺序逐条翻译执行。而C语言是编译型语言，需要被编译器整体编译后才能执行。
- 在没有续行符号 \ 的情况下，一行就是一条脚本。
- 任何一个shell脚本开头，都要用#！表示使用的解释器
- 脚本如果无法执行，有可能是权限不够，需要chmod
- shell语言本身不含linux的内置命令，如expr，这些命令本质是可以在shell环境中执行的程序，再shell环境下的shell脚本自然可以解释它们。

---
## $ 美元符
		这个符号再shell中是解释变量名，表示取值；
		和{}花括号共用，可以帮助解释器区变量名；
	   和()圆括号共用，可以表示去命令群组的执行结果。
	例如：

```shell
#!/bin/bash

var=123

echo "$varghj"
echo "${var}ghj"
echo "$(ls ./)"

```

![[Pasted image 20230310140941.png]]

		对于第一条命令，var和ghj连接，shell不能很好的区分，因而读到了空便量

---
## ()圆括号
		圆括号单独使用，指的是，在其包括的范围内自成一个设定，内外互不干扰.
		在数学计算时，圆括号则是优先计算括号内容
		与变量名=同用，将定义一个命令群组，（容器）
	例如：

```shell
#!/bin/bash

line="www"
(
	line="hhh"
	echo $line
)
echo $line

echo `expr \( 1 + 2 \) \* 3`

arry=(1 2 $line)
echo ${arry[2]}

```
![[Pasted image 20230310142358.png]]

_expr中的（）需要反斜杠转义！_

---

## $(())双重圆括号
		shell也支持简单和C语言相似的数学运算，但是需要加上$(())。支持16进制、二进制等。相同的逻辑效果可以使用 expr命令，但expr是linux的内置软件，并非shell的关键字。
		例如：

```shell
#!/bin/bash

ver=$((1+2))
echo $ver
ver=$((0x10+2))
echo $ver
#let可以代替(()),使得表达更接近C语言
let ver++
echo $ver
let ver+=1
echo $ver
let ver+=1
echo $ver

```

![[Pasted image 20230310174854.png]]


_双重圆括号一般只有进行数学运算的时候常用_

## [  ] 方括号
		方括号用作取容器的指定元素，另外还可以和$搭配，进行嵌套的数学解析
	例如：

 ```shell
 #!/bin/bash

#用做数组取指定元素
arry=(10 11 12 13 14)
echo ${arry[0]}

#用作嵌套运算
num1=1
num2=$[$num1+1]
echo ${num2}

```

![[Pasted image 20230310180006.png]]


## ``

		反单引号表示命令的替换，和$（）是等价的。但多层嵌套转义，还是$()更易懂。
		例如：
```shell
#!/bin/bash

i=10

echo `echo \`echo $i \``

echo $(echo $(echo $i))


```

![[Pasted image 20230313091739.png]]

_显然，单引号没有美元符+圆括号来的清晰_

---
## |
		管道指，前一个命令的返回结果，会作为后一个命令的参数。例如，我希望显示当前的路径下有几个文件。

```shell
#!/bin/bash

ls -l > text.txt

wc -l text.txt

```

![[Pasted image 20230313092639.png]]

_如此，可以得到路径下一共8个文件，但这样子，执行两个命令时，需要一个中间文件 text.txt_

_**利用管道，就可以一个命令行，执行两个命令**_
```shell
#!/bin/bash

ls -l | wc -l

#意思是，将 ls -l 的结果，作为 wc -l的参数

```

![[Pasted image 20230313093114.png]]
形式更加简洁了

----
## readonly
		相当于C语言中的const，readonly将变量设置称为只读模式，任何针对他们的辅助都是错误的。

```shell
#!/bin/bash
readonly data=10;
data=100;
```

![[Pasted image 20230313094448.png]]

---
## unset
		该关键字会将定义好的变量删除掉，再次调用这个变量，会因为没有定义而为空。如果该变量设置了readonly，则无法被unset访问删除。

```shell
#!/bin/bash
data=10;
echo $data
unset data
echo $data
```

![[Pasted image 20230313094952.png]]

- unset -v first/middle/last   可以删除特定位置的变量
- unset -f fun                               可以删除shell定义的函数

---

## shift
	shift可以删除传入参数。例如执行 ./*.sh 123 456，则对应的传入参数 ${0},${1},${2}为：
	*.sh
	123
	456
	可以使用shift删除掉 ${1}。但和unset的删除情况有所差异，输出${1}不会标识为空，而是${1}被${2}取而代之。以此类推，$#的值，会依次减1。
	shift固定只能删除 ${1}哦

```shell
#!/bin/bash
echo ${1}
shift 
echo ${1}
```

![[Pasted image 20230313095942.png]]

---

## set
		shift用来删除${1}的位置参数，而set可以设置全新的位置参数

```shell
#!/bin/bash
set qq www eee
echo ${0}
echo ${1}
echo ${2}
echo ${3}
echo $#

```

![[Pasted image 20230313100522.png]]


---
## export 
		export类似inclde但又不完全是。它的本意是为shell脚本配置环境变量。在 shell 中执行程序时，shell 会提供一组环境变量。export 可新增，修改或删除环境变量，供后续执行的程序使用。export 的效力仅限于该次登陆操作。

- export -f 代表变量名称中的函数名称
- export -n 删除指定的变量(实际没有删除，只是本次登录下，后续的shell命令不再认识这个变量)
- export -p  列出所有的shell赋予程序的环境变量(基本变量)


使用场景1：
有2个shell脚本
```shell
#以下是脚本1#test.sh
#!/bin/bash
export a="我是脚本1加入的变量a"
b="我是脚本1加入的变量b"
./test1.sh


#下面是脚本2#test1.sh
#!/bin/bash

echo $a
echo $b
a="666"
./test2.sh
#下面是脚本3#test2.sh
echo $a
```

		目前，再父进程shell1中，定义了变量a和b，其中变量a被定义为环境变量。在父进程中调用子进程shell2,子进程将获得父进程定义的环境参数，因此可以输出a。而b不是父进程定义的环境参数，因此子进程不能输出b。如果子进程修改了a，则子进程的子进程访问的a的参数也会被修改。他就像是一个进程下的线程们共同访问的全局变量一样。
		只有父进程shell定义的环境变量能被子进程shell所访问。子进程shell定义的变量，父进程shell是看不到的。
		

![[Pasted image 20230313104805.png]]


---
# 重定向
		平时使用的shell的输出结果，都是直接显示在stdout上。而通过重定向，可以将输出结果，输出在指定的位置。

## >
		以新建的方式重定向，如果文件不存在，就创建文件，覆盖写入

## >>
		以新建的方式重定向，如果文件不存在，就创建文件，追加写入

## 2>
		命令执行的错误输出结果重定向到指定的文件中，如果文件中包含了数据，会清空原有的数据

## 2>>
		将命令执行的错误结果重定向到指定的文件中，如果文件包含数据，追加写入

## <
		输入重定向，默认下是从stdin获取








