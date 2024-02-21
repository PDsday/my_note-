	Xaml将图形界面设计和界面交互逻辑分离开，艺术人寰可以独立的设计图形，而开发人员可以独立的编写代码。

---
### 1、顶层元素
> xaml文档进只用以下元素作为整个xaml树状结构的根节点或者说，顶级元素。
> - Window元素
> - Page元素(该元素和Window元素类似，但可以用于可导航的应用程序)
> - Application元素（该元素用于定义应用程序资源和启动设置）


### 2、InitializeComponent方法
	此方法为窗体的默认构造方法，永远不要删除在窗口构造函数中的此方法，同样，如果为窗口类添加了一个新的构造函数，也要确保调用InitializeComponent方法。
	该方法的内部调用来自 System.Windows.Application类的LoadComponnent方法，从程序集中提去XAML编译的文件BAML，并用它来构造用于界面，关联所有事件的处理程序。


### 3、字符串转义
	xaml的特性中，数值总是以一个纯文本字符串的形式出现。但是，对象的属性可以是任何的.Net架构中包含的类型，如常见的 int string double、或者更为复杂的枚举类型，复合类型。
	为了跨域字符串和非字符串属性之间的鸿沟，XAML解析器需要执行转换。转换由类型转换器执行。类型转换器自.net1.0以来，就已经存在于.net的基础结构中。

>如果你有兴趣了解：
>1、检查属性声明。在进行字符串到属性值的转换前，转换器会先对目标属性的属性声明做检查。
>例如背景属性ForeGround。检查到它的声明为 System.Windows.Color或者其他；
>2、如果检查不到属性的对应声明，将回去检查对应的数据类型声明；
>3、无法查询到任何声明将会赋值失败；
>4、类型转换器对字母的大小写是不敏感的，意味着 Foreground="white"/“White”的结果是相同的；


### 属性元素语法
	某些特定的属性或许不能通过直接对元素的 Property 进行赋值，例如一个渐变色的笔刷，就需要举例多个的色彩作为属性赋值，显然仅仅用字符串描述来实现是非常麻烦困难的。因此，XAML提供了属性元素语法，允许嵌套 Parent.PropertyName的方式进 Property进行赋值。
	例如:

```XAML
 <Path Stroke="{StaticResource RedToPurple}" StrokeThickness="20" >
    <Path.Data>
        <PathGeometry Figures="M 20,150 A 130,130 0 1 1 ,280 150"/>
    </Path.Data>
</Path>
```

	path.Data的赋值也就利用里这种属性元素语法。


### 标记拓展
	有时希望实现类似 C语言中 define宏定义得效果，用某一个特定名称去代替一长串得表达。XAML可以使用标记拓展实现。
	标记拓展可以用于嵌套标签或XML特性中。当用于特性中时，他们总是被花括号{}所包围。

### 附加属性
	除了普通属性外，XAML还包括一个附加属性 -- attached property 的概念。
	例如，将一个 TextBox控件放入已经布局好的Grid中，TextBox控件将可以获得属性 Grid.row、Grid.coulmn等本不属于textbox控件的属性。



