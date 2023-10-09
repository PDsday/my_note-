		ini文件和json文件一样，存在一定的格式，读取这些自带特殊格式的文件类型，C#有完善的库函数和方法。

```C
 [DllImport("kernel32")]//返回0表示失败，非0为成功
 private static extern long WritePrivateProfileString(string section, string key,
   string val, string filePath);
 [DllImport("kernel32")]//返回取得字符串缓冲区的长度
 private static extern long GetPrivateProfileString(string section, string key,
   string def, StringBuilder retVal, int size, string filePath);
```

		DllImort("kernel32")引入Win32的API函数，使用方法也较为简单。以一个ini文件为例子。

![[Pasted image 20230919145215.png]]

		FileSizeSet为一组数据的起始，他被成为section，区。一个区下有多个键值，如MainBordFileMax，他们为Key，每个Key一个值。但一个Key不一定就有值。

---
		获取一个键值直接使用 GetPrivateProfileString函数，他的参数为
		1、section，区名
		2、key，键值名
		3、def，当意图获取的键值没有数值时，返回此结果
		4、retval  StringBuilder类是用来虚拟一个string大小的，用以解决string开辟的堆区空间可能不足的问题，返回的结果会存在这个retval中
		5、size 指定是前一个参数，开辟的大空间字节大小
		6、ini文件的位置
		设置一个键值参数雷同。
		一般读写的部分可以参照如下，但不是完整的过程：

```C

//读
if (File.Exists(iniFilePath))//判断当前ini文件是否存在
{
    StringBuilder temp = new StringBuilder(1024);
    GetPrivateProfileString(Section, Key, NoText, temp, 1024, iniFilePath);
    return temp.ToString();
}
else
{
    return String.Empty;
}

//写
long OpStation = WritePrivateProfileString(Section, Key, Value, iniFilePath);
if (OpStation == 0)
{
    return false;
}
else
{
    return true;
}
```