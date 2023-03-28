>从官方github上clone下来的源码，编译过后空目录将写入很多内容

---
![[Pasted image 20230324093950.png]]

![[Pasted image 20230324094008.png]]

		按照图2的分级，tools，toolchain，include，scripts，target，package是原始目录
		也就是，默认情况下，从仓库上直接拉下来的部分。

![[Pasted image 20230324094158.png]]

		bin，build_dir，staging_dir，dl，feeds则是编译后产生的目录。

---
#### 目录简述

		1、tools，编译时需要的工具，一般都在tools下，里面是大量的Makefile，有的还有patch。每个Makefile里都有一句

![[Pasted image 20230324095436.png]]

		和HOST相关的 $eval。HOST在openwrt的编译中，被定义为编译主机。一般都是为了标识，编译这个工具是为了在主机上使用。

		2、toolchain，这个文件是工具链的库链接文件所在，也就是，能够在openwrt系统下运行的C库，内核头文件，什么的在这里面。但实际上也是一堆Makefile

![[Pasted image 20230324100228.png]]
![[Pasted image 20230324100504.png]]

		3、target 里面是firmware和kernel的编译过程定义。不同的架构商有不同的编译选择，这里面也能看见常见的老朋友。

![[Pasted image 20230324101309.png]]

		4、package openwrt的软件包，固件，驱动，用户自己写的package都丢在这里。openwrt定义了一套Makefile模板，各个软件参照这个模板定义，编译内核的时候，自己的package就能编译进去。
		5、include 更多的Makefile
		6、scrtipts 据说是make menuconfig时候的图形化插件，也就是perl脚本，负责软件包管理
		7、dl make联网下载的软件包压缩文件在这里
		8、build_dir make联网下载的软件包解压后放在这里
		9、 staging_dir 编译工具链，根文件等
		10、bin 编译完成后，firmware和各个ipk，系统镜像放在此处
		11、feed luci,好像是个很牛的东西。




