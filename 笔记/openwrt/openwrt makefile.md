
		一个标准的openwrt makefile包大概可以简化为：

 ```makefile
 		include $(TOPDIR)/rules.mk
		#这里会定义一堆PKG_XX，还不知道干嘛用的
		include $(INCLUDE_DIR)/package.mk
		#这里会定义各种package,build的宏
		$(eval$(call BuildPackage,包名))
```

#### 1、include $(TOPDIR)/rules.mk
		这一句将openwrt顶层目录下的 rules.mk的内容引进来，里面是预先定义好的许多宏定义。于编译规则相关，十分复杂，看不懂。

![[Pasted image 20230323172203.png]]
	
	  顶层目录下的makefile，定义了更多的宏，一些主要的宏就是顶层的makefile定义的。

![[Pasted image 20230323172447.png]]

		可以看见 TOPDIR就是当前目录的定义再定义。topdir,顶层目录。

#### 2、PKG_XXX
		这一部分是menuconfig菜单中的选项配置

```makefile
PKG_NAME XXXX #软件包的名字，在menuconfig和ipkg显示
PKG_VERSION   #软件包的版本
PKG_RELEASE   #软件包的makefile版本

 # 对于从git上下载的软件包，以下这些变量定义是必须的
    PKG_REV         -要下载的软件包在svn/git上的版本号
    PKG_SOURCE      -要下载的软件包的名字,一般是由 PKG_NAME 和 PKG_VERSION 组成
    PKG_SOURCE_URL  -下载这个软件包的链接
    PKG_SOURCE_SUBDIR   -源代码目录名
    PKG_SOURCE_VERSION  -源代码版本号，用于对从git下载的源代码进行MD5检查
    PKG_SOURCE_PROTO    -源代码的下载方式，即svn/git

    PKG_CONFIG_DEPENDS  -定义了选择这个软件包时指定的配置选项
    PKG_BUILD_DIR       -真正编译当前软件包的目录

```

		编写的hello_driver就不需要这么复杂
		
![[Pasted image 20230323173150.png]]

#### 3、include $(INCLUDE_DIR)/package.mk
		就是将$(TOPDIR)/include目录下的package.mk文件中的内容导入进来。
	   但这个package.mk的内容和rules.mk一样复杂又多还重要

>引进的一个比较重要的定义是：
>1、PKG_BUILD_DIR  make编译的过程中的临时编译目录

![[Pasted image 20230323174916.png]]
>是包编译前的转杯工作，我们创建一个临时的编译目录，然后把要编译的文件塞进去

#### 4、各种package,build的宏

		一个最简单的package就两种宏
		1、KernelPackage/Perpare
		2、KernelPackage/hellodriver
	   其他复杂的package包含的东西可能更多
	   比如：
	   1、Package/desription 软件包的详细说明
	   2、Package/Configure -源代码依赖的脚本调用
	   3、Package/Compile -源代码的编译规则
	   5、Packge/install - 软件包的安装方法，主要就是将一系列编译好的文件、启动脚本、UCI配置文件等拷贝到指定位置，

_Package并不是表述的唯一方式，有的叫做Build，有的是宏定义$(INSTALL_XXX)等，也是非常复杂的一个部分_

![[Pasted image 20230323175915.png]]

		hellodriver只做了3个定义，比较重要的是第一个和第二个。
		第二个的定义和menuconfig的显示相关

![[Pasted image 20230323180119.png]]