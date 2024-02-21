		典中典，又是这个，我都烦了

		和Linux的一切都是文件一样，python的文件接口概念也是差不多的，包括终端也是文件接口，希望从终端接收输入的函数为：

```python
raw_input()
input()
```

		前者会从终端中，也就是标准输入中读取一个行，并返回读取的结果(字符串形式)，并且，是没有行末的换行符的。
		而后者会将读取的数据，当作表达式来处理。
		但是可惜，python3之后，raw_input就被废掉了，而input变成raw_input的效果，不在将输入当作表达式解析，他成了完全的字符串。

```python
# python3后，rao_input就不在使用了，str = raw_input("请输入：") 届时将输入Hello Python  
print ("你输入的内容是: ", str)  
  
list = input("请输入:") #届时将输入[x*5 for x in range(2,10,2)]  
print(list)

```

---
### 1、打开文件
		先使用python内置的open()函数打开一个文件，创建一个file对象，相关的方法才可以调用它进行读写，这个open实际上和C语言的相同，获得的是文件描述符，但python将他封装成了一个类，和C++是一样的。

```python
file object = open(file_name [, access_mode][, buffering])
```

> 1、file_name：文件路径
> 2、access_mode：读取权限，可读只读，可写只写
> 3、buffering：建立读写缓冲区，文件较大的时间可以设置这样的缓冲区，提高读写的效率

		访问模式多种多样：

|模式|描述|
|---|---|
|t|文本模式 (默认)。|
|x|写模式，新建一个文件，如果该文件已存在则会报错。|
|b|二进制模式。|
|+|打开一个文件进行更新(可读可写)。|
|U|通用换行模式（不推荐）。|
|r|以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。|
|rb|以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。一般用于非文本文件如图片等。|
|r+|打开一个文件用于读写。文件指针将会放在文件的开头。|
|rb+|以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。一般用于非文本文件如图片等。|
|w|打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|wb|以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。|
|w+|打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|wb+|以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。|
|a|打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。|
|ab|以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。|
|a+|打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。|
|ab+|以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。|


		接下来是对文件读写的实例：

```python
#打开文件,追加模式，如果文件不存在则创建
file = open("./txt",a)
```

		其他操作用表列出

|序号|方法及描述|
|---|---|
|1|[file.close()](https://www.runoob.com/python/file-close.html)<br><br>关闭文件。关闭后文件不能再进行读写操作。|
|2|[file.flush()](https://www.runoob.com/python/file-flush.html)<br><br>刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入。|
|3|[file.fileno()](https://www.runoob.com/python/file-fileno.html)<br><br>返回一个整型的文件描述符(file descriptor FD 整型), 可以用在如os模块的read方法等一些底层操作上。|
|4|[file.isatty()](https://www.runoob.com/python/file-isatty.html)<br><br>如果文件连接到一个终端设备返回 True，否则返回 False。|
|5|[file.next()](https://www.runoob.com/python/file-next.html)<br><br>返回文件下一行。|
|6|[file.read([size])](https://www.runoob.com/python/python-file-read.html)<br><br>从文件读取指定的字节数，如果未给定或为负则读取所有。|
|7|[file.readline([size])](https://www.runoob.com/python/file-readline.html)<br><br>读取整行，包括 "\n" 字符。|
|8|[file.readlines([sizeint])](https://www.runoob.com/python/file-readlines.html)<br><br>读取所有行并返回列表，若给定sizeint>0，则是设置一次读多少字节，这是为了减轻读取压力。|
|9|[file.seek(offset[, whence])](https://www.runoob.com/python/file-seek.html)<br><br>设置文件当前位置|
|10|[file.tell()](https://www.runoob.com/python/file-tell.html)<br><br>返回文件当前位置。|
|11|[file.truncate([size])](https://www.runoob.com/python/file-truncate.html)<br><br>截取文件，截取的字节通过size指定，默认为当前文件位置。|
|12|[file.write(str)](https://www.runoob.com/python/python-file-write.html)<br><br>将字符串写入文件，返回的是写入的字符长度。|
|13|[file.writelines(sequence)](https://www.runoob.com/python/file-writelines.html)<br><br>向文件写入一个序列字符串列表，如果需要换行则要自己加入每行的换行符。|

