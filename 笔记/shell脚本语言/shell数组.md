>Bash shell只支持一维数组，初始定义不需要定义数组的大小哦，数组下标从0开始

---
		shell中的数组通过圆括号来表示（）

```shell
#!/bin/bash
#数据的分割用空格，如果用逗号会出现很特别的情况

arry1=(1 2 3 4 5)
arry2=(1,2,3,4,5)
arry3=(1,2,3,4,5 6)

#数组只通过空格分隔，arry2和arry3的差别看出，“1，2，3，4，5”当成了一个整体

echo ${arry1} 
echo ${arry2}
```

运行结果为：
![[Pasted image 20230302111218.png]]

---
			用逗号出现如此特备的情况的原因在于，shell的数组，是不限制元素的类型的，它的概念更倾向于C++的容器

```shell
#!/bin/bash
num=66
arry=(1 "haha" ${num})

echo ${arry[0]}
echo ${arry[1]}
echo ${arry[2]}
```
输出结果为：

![[Pasted image 20230302112405.png]]


_输出数组的某个元素，一样使用方括号+下标号的形式,但是，转义时一定要带上花括号，否则方括号会被认为是普通的字符，情况如下_

```shell
#!/bin/bash
num=66
arry=(1 "haha" ${num})

echo ${arry[0]}
echo $arry[1]

```
输出变成了这样：
![[Pasted image 20230302112620.png]]

_此外，还能验证一点，就是不加任何标号，直接转义数组名，是会得到数组的首个元素的_

### 如同字典的键与键值

		shell中支持一种关联数组，创建时，通过关键字 declare -A声明。这个数组就像是python的字典一样，但他并非通过关键字代替下标这么简单，达到访问指定数据的目的，例如下：

```shell
#!/bin/bash
declare -A arry=(["google"]="www.google.com" ["baidu"]="www.baidu.com")

arry[0]=0
arry[1]=1

echo arry[0]=${arry[0]}
echo arry["google"]=${arry["google"]}
echo arry[1]=${arry[1]}
echo arry["baidu"]=${arry["baidu"]}
```

![[Pasted image 20230302113731.png]].

		通过观察运行的结果,arry[0]!=arry["google"],因此，键值和下标并不是互相代替的关系。只是声明数组中一个变量的访问名。
		shell中有很方便获取数组大小的方式，这个大小指的是元素的个数：
		${#arry[*]}
		${#arry[@]}
		如果不带#号，则会打印出每一个元素。








