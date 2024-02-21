		pyhton的类定义如下：

```python
class ClassName:
	pass
```

		类的构造方法并非与类同名，而是：

```python
def __init__(self):
	pass

#self和this指针同理，python的类方法定义必须有一个self变量
#且self参数为第一个参数，但是self参数在类外调用的时候不需要传入
```



		python的类的继承并不写在冒号后面，而是如下声明

```python
class child(father):
	pass
```

		子类会继承父类的属性和方法，父子必须在同一个作用域内。如果父类来自另一个模块，要特别注意引入。

		 类中，所有以下划线开头的变量，均为私有变量

### 2、super
		super函数常见于有继承关系的类。
		在python中，每个父类的函数如果在子类里存在同名，子类调用时，父类的函数就会被覆盖，这是虚函数的概念。相比于C#和C++需要声明后才能顺利覆盖这个特点来看，python确实太自由。
```python
class A:
	def fun(self):
		print("A的fun")

class B(A):
	def fun(self):
		print("B的fun")


demo = B();
demo.fun();

```

1、如果没有定义B的 fun方法，运行时，将会想上匹配寻找，会找到A的fun方法并调用：
![[Pasted image 20231016122620.png]]
2、如果在B中定义了A的同名方法，A的方法就会被B的方法覆盖，运行时就会调用B的fun而不是A的：
![[Pasted image 20231016122727.png]]

		如果希望，B中定义fun方法的同时保留A的fun方法一起调用，可以使用super函数。

```python
class A:
	def fun(self):
		print("A的fun")

class B(A):
	def fun(self):
		super(B,self).fun()
		print("B的fun")


demo = B();
demo.fun();
```

		如此，子类调用fun函数就不会再覆盖父类。