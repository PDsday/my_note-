### 1、将图片添加到资源文件，Image控件引用

![[Pasted image 20230925120652.png]]

引用图片资源之后，利用如下函数：
```Cpp
public ImageSource SetImage(Bitmap Image)
{
    var image = Image;
    MemoryStream memory = new MemoryStream();
    image.Save(memory, System.Drawing.Imaging.ImageFormat.Png);
    ImageSourceConverter converter = new ImageSourceConverter();
    ImageSource source = (ImageSource)converter.ConvertFrom(memory);

    return source;
}
```

为Image控件添加Source图片这样调用即可：
```Cpp
this.img.Source = SetImage(WpfApp1.Properties.Resources._1693226770323);
```

		同样的，将字符串导入成资源文件，也就不用在内存中，费力定义大量的字符串。

