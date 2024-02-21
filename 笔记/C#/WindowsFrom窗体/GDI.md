		GDI是微软图形设备接口，能使应用程序能在视频显示器或者打印机上，使用图形和格式化文本。学习它能帮助理解如何重绘Winform的基本控件。
		GDI适用于所有Windows的应用程序。

---
### 1、位图
		位图是一个图形对象，用于创建、操作、以及将图像作为文件存储在磁盘上。一个位图对象由类 Graphic（画板）实例化而来。

|Graphic 对象|说明|
|---|---|
|[位图](https://learn.microsoft.com/zh-cn/windows/win32/gdi/bitmaps)|创建、操作 (缩放、滚动、旋转和绘制) ，并将图像作为文件存储在磁盘上。|
|[画笔](https://learn.microsoft.com/zh-cn/windows/win32/gdi/brushes)|绘制多边形、椭圆形和路径的内部。|
|[字体](https://learn.microsoft.com/zh-cn/windows/win32/gdi/fonts-and-text)|在视频显示器和其他输出设备上绘制文本。|
|[逻辑调色板](https://learn.microsoft.com/zh-cn/windows/win32/gdi/logical-palette)|由应用程序创建并与给定设备上下文关联的调色板。|
|[路径](https://learn.microsoft.com/zh-cn/windows/win32/gdi/paths)|填充和/或轮廓 (或形状) 的一个或多个图形。|
|[笔](https://learn.microsoft.com/zh-cn/windows/win32/gdi/pens)|应用程序用于绘制线条和曲线的图形工具。|
|[区域](https://learn.microsoft.com/zh-cn/windows/win32/gdi/regions)|一个矩形、多边形或椭圆 (或两个或更多个这些形状的组合，) 可以填充、绘制、倒排、定框，并用于对光标位置) 执行命|

		其中，画笔，笔，路径是重绘时常用到的信息。
		为控件插入图片生成资源时，也涉及到位图的概念，如果图片的位数偏低，图片的效果就和马赛克一样糊。
		接下来来详细的了解位图。

>位图：
>		从开发人员的角度来看，位图由指定或包含以下元素的结构集合组成：
>		1、标头，表述像素矩阵的分辨率，大小，深度(位数)等信息、
>		2、调色板
>		3、位数组，应以图像像素和逻辑调色板中的条目关系

位图不在深入，知道它时Windos GDI显示的原理就好。

---
### 2、画笔
		画笔是应用程序，用于绘制多边形、椭圆形和路径内部的图形工具。

