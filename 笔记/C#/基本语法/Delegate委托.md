		C#中指针认为是不安全的操作，因此不推荐使用指针。而函数指针也有了相应的替代----Delegate委托。
---
### 1、声明委托
		委托声明决定了可由该委托引用的方法，委托可指向一个具有相同标签的方法。
		假如有一个委托如下：

```C#
public delegate int function (string s);
```

		如上委托，该委托可以指向一个返回值是int，参数是一个字符串方法。同时，将方法指定委托不是像指针那样赋值，而是使用new语句。

### 2、实例
```C#
//声明一个委托
public delegate int function_deleaget(int n, int m);
class Main_program
{

        private static int num_add(int n, int m)
        {
            Console.WriteLine("加法");
            int num;
            num = n + m;
            return num;
        }

        private static int num_sub(int n, int m)
        {
            Console.WriteLine("减法");
            int num;
            num = n - m;
            return num;
        }

    static void Main(string[] args)
    {
        function_deleaget function_1 = new function_deleaget(num_add);
        function_deleaget function_2 = new function_deleaget(num_sub);

        function_1(10, 5);
        Console.Write("{0}", function_1(10, 5));
        Console.Write("{0}", function_2(10, 5));

        Console.WriteLine("hello word");
    }
}



```

### 3、多播委托

		委托支持“+”运算，实际上是调用了两个委托函数，例如上述代码

```C#

	function_delegate = function_n;
	function_n = function_1+function_2;
	Console.Write("{0}", function_n(10,5));

//两个结果是等效的，但打印只会打印function_2的结果
	

```
