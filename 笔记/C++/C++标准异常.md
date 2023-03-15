### try{  }    catch( )   throw 
----
- throw
> c++中可以采用抛出异常的方式去判断程序的错误。在c语言中，一旦程序发生了异常，会直接终止进程，而在c++中，我们有更高级的处理方式，也更贴近现实。

_关键字 throw可以抛出任意数据类型的异常_
_当程序发生了编写人员不希望看到的情况时，可以通过throw向外抛出异常_

#### 例如：
```cpp
//假设一个用于计算除法的函数，在数学要求中，除数不可为0，这是无意义的，因此，我们希望除数为0时，抛出一个异常。
//x为被除数，y为除数
void function(int x,int y)
{
	if(y==0)
	{
		throw "除数不可为0"；
	}
	return x/y;
}
```

>	当发生y=0的情况时，thorw会向外抛出异常，同时，这个除法函数也会因此终止，在其层层调用的关系中，没有任何函数处理这个异常，他最终会来到main函数，如果main函数也没有处理这个异常，这个进程将被终止。

_**处理throw抛出的异常需要用到函数catch()**_

---
- catch/try
>catch和try一起使用，常常能达到调试函数的效果。

#### 例如：
```cpp
int main()
{
	try
	{
		function(4,0);   //除数不能为0，这个函数执行后将会向外抛出异常
	}
	catch(const char* str)   //抛出的错误是一连串字符串，参数也为字符串
	{
		cout<<str<<endl;
	}
}

/*
	当无法判断会抛出什么样的异常时，可以使用
	catch(...)
	{
	
	}
	这个格式支持捕获所有类型的异常。
*/

```

>      - throw支持抛出的异常类型是任意的，甚至是自定义的类，结构体，同样能够抛出。
>      - 如果发生throw的函数中已经存在catch，并将其捕获，则这个异常不会对外传递。

- [i] catch可以有很多很多，甚至一层套一层

#### 例如：
```cpp
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::string;

//函数1，抛出一个整数异常
void fuction1()
{
	throw 1;
}

//函数2，抛出一个浮点数异常
void fuction2()
{
	throw 1.5;
}

//函数3，抛出一个字符串异常
void fuction3()
{
	throw "我是异常"；
}


int main()
{
	try
	{
		fuction1();//fuction2()/fucntion3()
	}
	catch(float f)
	{
		cout<<"捕获到浮点数异常"<<endl;
	}
	catch(int i)
	{
		cout<<"捕获到整数异常"<<endl;
	}
	catch(const char* str)
	{
		cout<<"捕获到字符串异常"<<endl;
	}
	catch(...)
	{
		cout<<"未知异常"<<endl;
	}
	
}

/*
	抛出的异常会被最契合的catch所捕获，但如果没有契合的catch，就会被catch(...)捕获
	catch(...)必须写在其他的catch之后
*/

```

- [x]  
    _问题：当发生一个throw时，如果异常立刻被catch捕获，这个函数还会被终止吗？_<aside><h1> 不会，这个函数会接着catch之后，继续运行</h1></aside>
    
 
  
---

### C++的标准异常类

>  _**C++提供了标准异常类 exceptio，他本身可以用于捕获所有的异常，原理是[[类的继承]]和[[多态]]中的虚函数**_

#下面是一个使用范例：

```cpp
#include <iostream>
#include <exception>   //c++标准异常类头文件
#include <string>

using namespace std;

class my_error :public execption   //公有继承，我们将重写其中一个虚函数
{
	public:
	my_error(string msg)
	{
		this->msg = msg;
	}
//execption类中，存在一个函数 what(),我们可以使用虚函数覆盖父类的what(),
//输出自己想要的结果
	virtual const char* what()const
	{
		return msg.c_str();
	}
	private:
		string msg;
}

int main()
{
	try
	{
		my_error A("我是自定义的异常")；
		throw A;   //此处理应使用catch,捕获my_error类的变量，但使用了虚函数
				   //可以使用多态的方式，通过父类的引用或者指针，捕获子类		
	}
	catch(exception &e)
	{
		cout<<e.what()<<endl;
	}
}
```
       标准异常类的加入，使得，无论以后新加入多少类，只需要继承execption，并重写what(),就可以使用一个统一的模板去直接捕获这一类的异常，非常方便。