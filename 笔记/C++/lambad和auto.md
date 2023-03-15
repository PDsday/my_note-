#### _**lambad和auto，主要用于提升写代码的效率**_
---
# lambad
-      匿名函数：lambad函数，又叫做匿名函数，对于非常短小，且使用频率不高的函数，另外封装似乎有些多余，而使用lambad函数，可以轻松解决这个痛点。

_lanmbad使用的格式为：[实参列表]（形参列表）->返回值类型 {函数体}；_

### 过去在C语言中的实现：
```c
//例如，交换两个数据
void swap(int *a,int *b)
{
	*a=*a+*b;
	*b=*a-*b;
	*a=*a-*b;
}

//在另一个函数中，通过函数指针调用交换,需要创建一个函数指针指向swap
void handler(int *a,int *b,void (*fun)(int*,int *))
{
	fun(a,b);
}

int main()
{
	int a = 10;
	int b = 15;
	int (*fun)(int*,int *) ptr = swap;
	handler(&a,&b,swap);
	printf("%d %d\n",a,b);
}
```

### 在c++中使用匿名函数实现：
```cpp
//使用引用更为方便，同时采用匿名函数的方式
//不需要多写一遍 swap
void handler(int &x,int &y,void(*fun)(int&,int&))
{
    fun(x,y);
}


int main(int argc, char const *argv[])
{
    int a = 10;
    int b = 15;

    handler(a,b,[](int&x,int&y)->void{x=x+y;y=x-y;x=x-y;});

    std::cout<<"a:"<<a<<" b:"<<b<<std::endl;
    
    return 0;
}

//可见减少了一次函数的封装
```

---
---


# auto
-     auto称为自动类型，编译器会根据赋值，自动赋值变量的最优类型。
_使用方法同其他变量类型一样，如 auto a;_

### 例如：
```cpp
int main()
{
	auto a = 10;   //a会自动获得int类型
	auto b = 1.5;  //a会自动获得double类型 

	//甚至
	auto ptr = new auto(9); //获得 int*

	//lambda例子中，甚至可以这样子生成函数指针
	auto fun = [](int&x,int&y)->void{x=x+y;y=x-y;x=x-y;};

	return 0;
}
```

_**auto对于主要用C编程的程序员来说，也许并不是很值得推荐，但他在简化复杂类型的变量声明时，非常实用**_

      使用auto需要注意：所有通过auto赋值的变量一定要在声明时直接初始化，否则编译器无法识别变量的类型。


- 通过auto去实例化自定义的类时，同样会调用[[类的构造和析构]],使用方式为：
```cpp
class A
{
    public:
    A(){std::cout<<"A的构造"<<std::endl;}
    ~A(){std::cout<<"A的析构"<<std::endl;}
};

int main(int argc, char const *argv[])
{
    auto c = A();    //调用类的构造，通过auto创建对象 
    return 0;
}
```


