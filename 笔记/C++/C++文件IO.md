C++文件IO使用数据流的方式，相关的库为 < fstream >

>即使是文件读写，依旧是鲜明的C++的类与对象的风格，相比于C基本IO的文件描述符和标准IO的文件流，C++的文件读取对象通过[[类的构造和析构]]进行open/fopen以及close和fclose。

### 打开文件的mode
>    不论读写，其构造函数的两个参数分别为 文件路径path，读写模式mode
>    在文件读写流常用的模式有
>    _std::ios::in_  : 只读（ifstream）
>    _std::ios::trunc_ : 只读（ofstream）
>    _std::ios::out_ : 只写，且光标位于文件起始位置
>    _std::ios::ate_ : 追加，光标位于文件末尾
>    _std::ios::app_ : 追加，只有在文件进行写操作的时候，才会将光标移动到文件末尾
>    _std::ios::binary_ :二进制的方式打开文件
>     

### ifstream 文件读取类
- 看个例子：
```cpp
void read_file()
{
    ifstream in("file.txt",ios::in);
    if(!in.is_open())
    {
        cout<<"文件打开失败"<<endl;
        return ;
    }
    char buffer[256]={0};
    //in.read(buffer,256);   //这种读会读取指定字节长度
    while(!in.eof())
    {
        in>>buffer;             //这种读遇到空格和换行将结束
        cout<<buffer<<endl;
    }
}
```

### ofsream文件写入类
- 看个例子：
```cpp
void write_file()
{
    ofstream out("file.txt",ios::out);//ios::ate追加

    char buffer[] = "!!!";
    out<<buffer;
}
```


----

#### ifstream的成员函数
- is_open()                  判断文件是否正确打开
- eof()                            是否读到文件末尾
- read(const char * buffer,size_t size)    读取指定字节大小
……用到其他的再补充

#### ofsream的成员函数
-  is_open()                  判断文件是否正确打开
- write(const char * buffer,size_t size)  写入指定字节大小
……用到其他再补充