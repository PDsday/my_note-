>首先SDK就是很大的软件包，全名是 Soft Development Kit的缩写，译为“软件开发工具包”。通常是为了辅助开发欧蕾软件而编写的特定软件包，包含相关的文档，范例和工具。

---
### SDK有两种
##### 1、系统SDK
![[Pasted image 20230322143240.png]]
>正在编译的这个就是系统SDK，是为特定的软件包，软件框架，硬件平台，操纵系统等建立应用时，所使用的开发工具合集。本质上，是一个大大的软件的合集。
>目前正在编译的系统SDK，是openwrt linux MTK-ARM aarch64 的产品。

##### 2、应用SDK
>应用SDK就是常说的固件了，它是基于系统SDK开发的独立于具体业务而具有特定功能的集合。
>暂时没有例子，他不是驱动，也不是API。

		如何去理解这些SDK呢？一个SOC厂商，将产品生产出来，必定是经过测试的。以树莓派为例，一个树莓派开发环境，需要购买树莓派的用户，从编写固件开始吗？不需要了。树莓派厂商自身会提前提供好固件。
		可以这么联想。厂商生产了插座，二次开发的厂商，去要生产和插座适配的插头。二次厂商，是否需要拿到测试插座后，从投开发插头呢？
		这显然很难。而初始厂商，为了测试插座的性能，一定是有插头的模板的。这个模板，二次厂商可以沿用，在这个模板上二次开发。大大缩短了开发的时间。这个模板就是SDK了。应用SDK，是对这个模板的二次开发，添加的新东西。比如，我想这个插头，不仅仅是插头，我希望他还能播放音乐，播放视频，因此，我们在拿到的系统SDK(模板)上，大刀阔斧的改革，添加，这就是应用SDK。



