		linux shell可以用户自定义函数，然后再shell脚本中可以随意调用。

---

		function(){
			action
			return number
		}
	1、shell函数的返回值必须是数字。
	2、如果要给函数传递参数，不需要声明，函数内部通过${x}，类似shell变量传递的方式
	3、调用函数直接写函数名即可
	4、函数调用后，立即使用$?可捕获函数的返回值

```shell
#!/bin/bash

#!/bin/bash

fun1()
{
    echo "我是test.sh中的函数1"
}

fun2()
{
    echo "我是test.sh中的函数2,返回输入的和"
    echo "参数1是${1}"
    echo "参数2是${2}"
    return $(( ${1} + ${2} ))
}

#直接调用
fun1
fun2 1 2
echo $?
```

_函数使用echo返回，可以让函数的返回值为字符串_
```shell
#!/bin/bash
fun()
{
	first_name=$1
	last_name=$2
	family_name=$3
	echo $first_name
	echo $last_name
	echo $family_name
}

#利用$？
res=$(fun tony static L)

```

运行发现没有输出结果，因为结果已经被res收取
添加一句
_**echo $res**_
结果为：![[Pasted image 20230313113044.png]]


## 在其他的脚本中调用shell函数

```shell
#例如，有连个脚本 test.sh  test1.sh

#test.sh如下
#!/bin/bash
fun_add()
{
	return $(( ${1} + ${2} ))
}

#在test1.sh下
#!/bin/bash
source ./test.sh
fun_add 1 2
echo $?
```

_source命令将其他的脚本加入此处脚本_

![[Pasted image 20230313114038.png]]

