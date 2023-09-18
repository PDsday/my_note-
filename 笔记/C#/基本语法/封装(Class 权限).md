
		面向对象的定义中，使用namespace，函数，类等等行为都是一种封装，并且封装后可以增加权限。
		1、public 公开，
		2、private 私有，仅有类对象自身可以访问
		3、protected 保护，只有类对象本想和自己的子类可以访问
		4、internal
		5、protected internal 

----
前三个并不陌生，主要是两个新的关键字
#### internal
		用这个描述符，可以将类内的成员变量暴露给这个文件下其他的类或者封装。可以直接理解为，在当前文件下，视为public,在其他文件，视为private。

#### protected internal
		其他文件的，和这个类有关的子类，也可以访问了。

博客园的描述特别有趣：

1.   
    2142
    
    比如说：一个人A为父类，他的儿子B，妻子C，私生子D（注：D不在他家里）
    
    如果我们给A的事情增加修饰符：
    
    -  public事件，地球人都知道，全公开
    -  protected事件，A，B，D知道（A和他的所有儿子知道，妻子C不知道）
    -  private事件，只有A知道（隐私？心事？）
    -  internal事件，A，B，C知道（A家里人都知道，私生子D不知道）
    -  protected internal事件，A，B，C，D都知道,其它人不知道
    
    sally44
    
       sally44
    
      sal***an44@163.com
    
       [参考地址](http://blog.csdn.net/jadeflute/article/details/5019478)
    
    6年前 (2017-12-04)
    
2.    樱花树
    
      100***2797@qq.com
    
    511
    
    -  (1) Pubilc ：任何公有成员可以被外部的类访问。
    -  (2) Private ：只有同一个类中的函数可以访问它的私有成员。
    -  (3) Protected ：该类内部和继承类中可以访问。
    -  (4) internal : 同一个程序集的对象可以访问。
    -  (5) Protected internal ：3 和 4 的并集，符合任意一条都可以访问。
		