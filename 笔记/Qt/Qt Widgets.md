>Qt Widgets是Qt最常用的类，他是Qt已封装的一个窗口，通过这个类我们的程序运行时，可以生成一个弹窗。Widgets也是Qt最初级的ui，在它的基础上，派生出来各种其他的高级ui，如显示数据，状态的文本框、接受用户输入的互动等等。

### 1、帮助文档中的Qt Widgets
		学习Qt从学习使用帮助文档开始，右击帮助，在目录栏中找到Qt widgets一栏，点击即可看到帮助文档

![[Pasted image 20221101185517.png]]

### 2、使用方法
![[Pasted image 20221101185604.png]]

_**通过帮助文档可知，使用Qtwidgets类，需要添加头文件 #include< Qtwidgets >，同时在工程文件.pro添加QT += widgets，才可以使用。**_


### 3、测试
创建一个空的qmake项目
![[Pasted image 20221101192052.png]]


一路默认之后会出现一个空的工程文件
工程文件添加以下信息：
```make
QT       += core gui

greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

CONFIG += c++11
```

然后创建c++ sourcefile
右击文件项目栏，选择add New

![[Pasted image 20221101192342.png]]

选择c++surce File，同时添加到对应工程文件
![[Pasted image 20221101192425.png]]

设置完毕后，会发现工程文件自动添加了这几行
![[Pasted image 20221101192456.png]]

#### 通过QtWidgets类设置一个最初始的窗口界面
```cpp
#include <QApplication>          //如果工程文件没有正确加入上述三句，添加
#include <QtWidgets>             //头文件会报错

	//QApplication为最基本框架，不能少
	//通过帮助文档可知使用QtWidgets，需要添加该头文件
int main(int argc, char *argv[])
{
    QApplication a(argc,argv);
    
    QTabWidget Windows1;   //实例化一个窗口类
    Windows1.setWindowTitle("这是最初始的窗口"); //这个行为可以设置窗口的名字
    Windows1.show();//添加这个才能正常的显示窗口，不然qmake也会报异常
    
    return a.exec();
}
```

# ！QtWidgets的派生类！
>QTWidgets实在是太基本了，一般我们使用他的派生类，而它的派生了就丰富多样了

帮助文档路径为 _**QT Widgets -> API Reference -> Qt Widgets C++ Classes**_
这个路径下基本能看到所有的派生类了。这里介绍几个基本的派生类

- QMainWidow（主窗口类）
- QLineEdit (对话框类)
- QPushButton(按钮类)

使用时，添加同名的头文件即可
通过C++程序创建一个登录界面

```cpp
//创建一个登录界面
#include <QApplication>
#include <QtWidgets>
#include <QMainWindow>
#include <QPushButton>
#include <QLineEdit>

int main(int argc, char *argv[])
{
    QApplication a(argc,argv);
    QMainWindow Windows1;
    Windows1.setWindowTitle("这是最初始的窗口");
     Windows1.show();
    //用户名和用户密码的输入对话框
    QLineEdit usrname(&Windows1);   //这样子实例化，会使得Windows1作为此对话框的一级窗口
    usrname.setGeometry(10,10,100,30);//分别设置这个对话框相对于前一级的对话框的几何位置和大小
    usrname.show();

    QLineEdit password(&Windows1);
    password.setGeometry(10,50,100,30);
    password.show();

    QPushButton login(&Windows1);
    login.setGeometry(10,100,40,30);
    login.show();

    QPushButton Register(&Windows1);
    Register.setGeometry(60,100,40,30);
    Register.show();

    return a.exec();

}
```

### 通过ui可以快速的创建一个自定义的窗口并使用

#### 1、首先创建一个基本的Qt项目文件
![[Pasted image 20221101213545.png]]
主角便是这个ui文件，双击它。
#### 2、依据需求设置窗口大小，拖拽需要的组件即可

![[Pasted image 20221101213827.png]]

#### 3、相应的ui文件会生成同名的.h文件，在主函数中囊括即可

![[Pasted image 20221101214023.png]]
#### 4、然后直接编译
![[Pasted image 20221101214110.png]]
相比于使用C++调用，ui文件更快，更便捷。

		ui文件的创建过程也是耐人寻味的。
		1、首先，在Qt内部，创建的ui文件在编译是会变成.xml文件，
		2、然后Qt将xml文件翻译成.h文件，这是我们调用的关键。
		3、当我们创造同名的窗口类时，实际上内部发生了一次较为复杂的替换。

##### 来到与ui文件同名的.cpp源码文件，这里有一句关键代码
![[Pasted image 20221101214640.png]]
在这里，this是什么呢？
右击setupUi函数，选择 follow symbol under  cursor 可以看到它的源码。
![[Pasted image 20221101215017.png]]


可见，传进来的 this 描述为 MainWindow。他就是QMainwindow。在这里，这个实例化的主窗口，被一个 centralwidgets不断的替换。
这个 centralwidgets就是我们ui设计的窗口，它最终将一个空白的主窗口覆盖，得到了我们设计的窗口。

---

### QWidgets的派生类的ui调节

在ui界面默认还可以对相应的窗口进行深度定制
![[Pasted image 20221101232606.png]]

点击对应的组件(控件)，即可深度定制他们
在这里可以看见前面描述的，centralwidget和MainWindow的覆盖关系。

#### 0、QMainWindow定制
点击QMainWindow组件，在属性上可以看到类的派生关系
![[Pasted image 20221101233056.png]]
修改自身类的属性，或者从父类继承下来的属性
如：
- 组件大小
![[Pasted image 20221101233538.png]]
- 鼠标进入窗口时光标的形状，字体的大小
 ![[Pasted image 20221101233629.png]]
 

#### 1、LineEdit定制
点击任意一个LineEdit组件，在属性上可以看到类之间的派生关系
![[Pasted image 20221101232750.png]]
- 输入的最大字符个数
![[Pasted image 20221101233759.png]]
- 边框隐藏或者显示
![[Pasted image 20221101233836.png]]
- 文本的对齐方式
![[Pasted image 20221101233931.png]]
			如图是居中对齐

- 默认文本，在没有数据输入是会显示

![[Pasted image 20221101234030.png]]



#### 2、QPushButton定制
- 边框隐藏或者显示
![[Pasted image 20221101234145.png]]
- 默认相应按键
![[Pasted image 20221101234222.png]]
