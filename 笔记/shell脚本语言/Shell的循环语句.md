> shell 也有 for循环和while循环，用法接近python而是不C语言。还有一种特别的循环 until

---
## for
		for的运行方式是从串行列表中，将里面的元素一一取出，依序放到指定的变量中。然后重复执行命令区域。在C语言中，代码块用花括号包裹，而在脚本语言中，它使用 do 和 done包裹代码块。

##### 串行列表
		它和数组相同，但一定是纯数字的。
		可以人为定义一个串行列表
		也可以使用关键字 seq 生成一个等差列表

```shell
#!/bin/bash
#for 参变量 in 串行列表 
#do
#代码块(循环体)
#done

#可以是
List=(1 2 3 4 5)
for i in ${List[@]}
do
    echo $i
done

#可以是
for i in $(1 2 3 4 5)
do
    echo $i
done

#可以是
for i in $( seq 1 5 )
do
    echo $i
done

#甚至
NameList=(小明 小红 小哈)
for i in ${NameList[@]}
do
    echo $i
done

#for同样是可以嵌套的，配合一些原生命令，会有意想不到的效果


#定义一个路径字符串
DIR="./"
#关键字 cd
cd $DIR
for i in $(ls $DIR)
do
	[-d $i] && du -sh $i
done
```

## while
		while判断和C语言相同，条件为真则执行循环体，条件为假则跳出循环
		while 条件
		do
			循环体
		done

```shell
#!/bin/bash
#最常见的用法就是使用死循环，不断的读取文件的内容

while read kuangl
do
    echo ${kuangl}
done < ./text.txt 

```

如果目录下有一个./text.txt，里面的内容会被读取
![[Pasted image 20230310135107.png]]


## until
_用法和while是一摸一样的，唯一的区别是，条件_
_while只有条件为真才会执行循环体，而until则是条件为假才会执行循环体_

