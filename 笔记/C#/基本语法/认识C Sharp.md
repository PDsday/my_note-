		C#是一个面向对象的编程语言，由微软开发，构想上十分接近C和C++，又与Java非常相似。
		1、布尔条件
		2、标准库
		3、组件
		4、属性
		5、委托
		6、简单多线程
		----
	C#是.Net框架的一部分，用于编写.Net应用程序。

# .NET框架(.Net Framework)

		.Net框架是一个创新的平台，能够用来设计Widnos应用程序，Web应用程序，或者Web服务。可以将.Net理解为一个巨大的代码库，用于C#等客户端语言。非常接近Qt的框架和组件的概念。
		C#的集成开发环境可以使用Visual Studio。

# C#的程序结构
>一个C Sharp程序主要包括：
>1、命名空间生命(命名空间的概念和Cpp相同)
>2、一个Class（同Cpp）
>3、Class属性（同Cpp）
>4、一个Main方法(main函数)
>C#的文件后缀为.cs

```C#
using System;
namespace Project1
{
    class helloword
    {
        static void Main()
        {
            Console.WriteLine("Hello World");
            Console.ReadKey();
        }
    }
}
```

		using是指启用某个命名空间，和Cpp一样，只有在该文本using某个命名空间，才能无需空间名，直接使用空间下类的成员和方法，如果不适用using System，那么代码要这么写。

![[Pasted image 20230829155516.png]]

---

		Class，类，概念和Cpp是一样的。类包含多个方法，方法定义了类的行为。Main函数也是这个类的一个方法。同时又是整个程序的唯一入口。
		Console.WriteLine可以理解为 std::out
		Console.ReadKey相当于system("pause");