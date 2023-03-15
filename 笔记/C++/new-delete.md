>C++堆区操作有两个全新的操作运算符。new_delete。这两个运算符的内部是malloc，free，底层依旧是内核函数。new_delete和malloc_free最大的区别在于，操作类对象时，前者会调用[[类的构造和析构]]，能对堆区开辟的空间进行初始化，而malloc_free仅仅是开辟/释放堆区。因此，new和delete要更严谨，操作上也更安全。

---
### 常用操作
```cpp

//在c语言中，我们如此开辟一个4字节的堆区空间
int *ptr = (int *)malloc(sizeof(int));

//在c++中，使用new
int *ptr2 = new int;

//可以初始化
int *ptr2 = new int(10);

//开辟数组
int *ptr2 = new int[10];

//数组也可初始化
int *ptr2 = new int[10](0);

//释放
delete ptr2;

//释放数组
delete []ptr2;

```

new和malloc的区别
- malloc是库函数，而new是运算符，前者需要头文件支持，后者需要编译器支持
- malloc不在编译器的控制权限之内，不能强加初始化操作，而new可以调用构造函数
- malloc返回是void* 地址，new则严格对应
- 开辟失败时，new会抛出bac_alloc异常，而malloc会返回NULL
- new/delete是运算符，是允许重载的
- new申请的内存应该称为自由存储区，在堆的基础上建立但不全等于堆，是两个概念