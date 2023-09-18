		FileSystemInfo类是任何文件系统类的基类，FileInfo和file表示系统中的文件，DirectoryInfo于Directory表示文件系统中的文件夹，Path表示文件路径，DriveInfo提供有关驱动器的信息访问。
		带有Info的类才可以实例化，不带Info的类是抽象类。


### 1、FileInfo

```C#
FileInfo file = new FileInof("file_path");
//fileinfo不会在乎文件存在还是不存在
//并且，只有当写入文件后，文件才会被创建，单独调用这个构造类，没有任何写入的操作是不会创建文件的，如果想要强行创建一个空文件，使用方法Create

Console.WriteLine("创建时间：" + file.CreationTime);
Console.WriteLine("路径：" + file.DirectoryName);
file.create();

Console.WriteLine("创建时间：" + file.CreationTime);
Console.WriteLine("路径：" + file.DirectoryName);

//可以直接获取文件的创建时间和具体路径，但时间是没有经过修正的。这是因为文件其实目前根本不存在，需要调用create拥有实体后，才会有准确的创建时间。

```

![[Pasted image 20230905144127.png]]

```C#

StreamWriter sw = file.AppendText();//打开追加流
sw.Write("李志伟");//追加数据
sw.Dispose();//释放资源,关闭文件
//写入这个文件，需要打开文件追加流，这个追加写入实在问津已有的内容之后追加，不会覆盖原来的内容

StreamWriter sw = file.CreateText();
sw.Write("123456");
sw.Dispose();
//新的数据流会对旧的数据进行覆盖

```

其他用法见下表：

|属性|描述|
|:---:|:---:|
|Attributes|获取或者设置当前文件或者目录的属性|
|CreationTime|获取文件或者目录的创建时间|
|Directory|用于获取父目录的实例|
|DirectoryName|用于获取表示目录完整路径的字符串|
|Exists|它用于获取一个值，指示文件是否存在|
|FullName|它用于获取目录或文件的完整路径|
|IsReadOnly|设置当前文件为只读，或者确定当前文件是否为只读|
|LastAccessTime|获取或者设置从上一次访问当前文件或目录的时间|
|Length|获取当前文件的字节数|
|Name|获取文件的名称|

|方法|描述|
|:---:|:---:|
|AppendText()|它用于创建一个`StreamWriter`类对象，将文本附加`FileInfo`的此实例所表示的文件中。|
|CopyTo(String)|它用于将现有文件复制到新文件。|
|Create()|它用于创建文件。|
|CreateText()|它用于创建一个写入新文本文件的`StreamWriter`类对象。|
|Decrypt()|它用于解密由使用加密方法当前帐户加密的文件。|
|Delect|它用于永久删除文件|
|Encrypt()|它用于加密文件，一边只有用于加密文件的账户才能对其进行解密|
|GetAccessControl()|获取封装访问控制列表的`FileSecurity`对象|
|MoveTo(String)|将文件移动到新的位置|
|OpenRead()|创建只读的`FileStream`|
|OpenText()|创建从现有文本文件使用UTF8编码读取的`StreamReader`类|
|OpenWrite()|它用于创建只写`FileStream`对象|
|Refresh()|刷新对象的状态|
|Replace(String,String)|使用当前`FileInfo`描述文件替换指定文件的内容|
|ToString()|用于将路径作为字符串返回|
###### 简单读写实例
```C#
            FileInfo file = new FileInfo("test.txt");
            StreamWriter sw = file.CreateText();
            sw.WriteLine("赵客缦胡缨，吴钩霜雪明");
            sw.WriteLine("银鞍照白马，飒沓如流星");
            sw.WriteLine("十步杀一人，千里不留行");
            sw.WriteLine("赵客缦胡缨，吴钩霜雪明");
            sw.WriteLine("事了拂衣去，深藏身与名");
            sw.WriteLine("闲过信陵饮，脱剑膝前横");
            sw.WriteLine("将炙啖朱亥，持觞劝侯嬴");
            sw.WriteLine("三杯吐然诺，五岳倒为轻");
            sw.WriteLine("眼花耳热后，意气素霓生");
            sw.WriteLine("救赵挥金槌，邯郸先震惊");
            sw.WriteLine("千秋二壮士，烜赫大梁城");
            sw.WriteLine("千秋二壮士，烜赫大梁城");
            sw.WriteLine("纵死侠骨香，不惭世上英");
            sw.WriteLine("谁能书阁下，白首太玄经");
            sw.Close();
            StreamReader sr = file.OpenText();
            Console.Write(sr.ReadToEnd());  
```

---


### 2、DirectoryInfo和Directory类
```C#
DirectroyInfo directory = new DirectoryInfo("directory_path");
//同样，仅仅实例化并不会创建一个文件夹
directory.Create(); //调用Create方法强行创建一个空文件夹。

Console.Write(directory.FullName);
//FullName用以返回绝对路径的字符串

FileSystemInfo[] files = directory.GetFileSystemInfos();
foreach(FileSystemInfo fs in files)
{
	Console.WriteLine(fs.Name);
}
//用于获得目录下的所有文件与文件夹的实例化

```

		常用的属性如下:

|属性|描述|
|:---:|:---:|
|Attributes|它用于获取或设置当前文件或目录的属性。|
|CreationTime|它用于获取或设置当前文件或目录的创建时间。|
|CreationTimeUtc|它用于在协调的世界时间(UTC)中获取或设置创建时间。|
|Exists|它用于获取一个值，指示目录是否存在。|
|Extension|它用于获取表示文件扩展部分的字符串。|
|FullName|它用于获取目录的完整路径。|
|LastAccessTime|它用于获取或设置上次访问当前文件或目录的时间。|
|LastAccessTimeUtc|用于在当前文件或目录被上次访问的协调世界时间(UTC)中获取或设置时间。|
|LastWriteTime|它用于获取或设置当前文件或目录最后写入的时间。|
|LastWriteTimeUtc|当最后一次写入当前文件或目录时，它用于在协调世界时(UTC)中获取或设置时间。|
|Name|它用于获取此`DirectoryInfo`实例的名称。|
|Parent|它用于获取指定子目录的父目录。|
|Root|它用于获取目录的根部分。|

		相关方法

|方法|描述|
|:---:|:---:|
|Create()|它用于创建一个目录。|
|Create(DirectorySecurity)|它用于使用`DirectorySecurity`对象创建目录。|
|CreateObjRef(Type)|它用于创建一个对象，其中包含生成用于与远程对象通信的代理所需的所有相关信息。|
|CreateSubdirectory(String)|它用于在指定的路径上创建一个子目录或子目录。|
|CreateSubdirectory(String,DirectorySecurity)|它用于在具有指定安全性的指定路径上创建子目录或子目录。|
|Delete()|如果`DirectoryInfo`为空，则用于删除`DirectoryInfo`。|
|Delete(Boolean)|它用于删除`DirectoryInfo`的实例，指定是否删除子目录和文件。|
|EnumerateDirectories()|它返回当前目录中可用的目录信息集合。|
|EnumerateFiles()|它在当前目录中返回无数的文件信息集合。|
|GetAccessControl()|它用于获取一个`DirectorySecurity`对象，该对象封装了目录的访问控制列表(ACL)条目。|
|GetDirectories()|它返回当前目录的子目录。|
|GetFiles()|它从当前目录返回文件列表。|
|GetType()|它用于获取当前实例的类型|
|MoveTo(String)|它用于将`DirectoryInfo`实例及其内容移动到新路径。|
|Refresh()|它用于刷新对象的状态。|
|SetAccessControl(DirectorySecurity)|它用于设置`DirectorySecurity`对象描述的访问控制列表(ACL)条目。|
|ToString()|它返回用户传递的原始路径。|

---
```C#

//超多以后不知道会不会用到的东西

(4)Path类
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine(Path.Combine(@"E:\学习笔记\C#平台", @"Test\Test.TXT"));//连接
        Console.WriteLine("平台特定的字符：" + Path.DirectorySeparatorChar);
        Console.WriteLine("平台特定的替换字符：" + Path.AltDirectorySeparatorChar);
        Console.Read();
    }
}
(5)DriveInfo类
class Program
{
    static void Main(string[] args)
    {
        DriveInfo[] drives = DriveInfo.GetDrives();
        foreach (DriveInfo d in drives)
        {
            if (d.IsReady)
            {
                Console.WriteLine("总容量：" + d.TotalFreeSpace);
                Console.WriteLine("可用容量：" + d.AvailableFreeSpace);
                Console.WriteLine("驱动器类型：" + d.DriveFormat);
                Console.WriteLine("驱动器的名称：" + d.Name + "\n");
            }
        }
        Console.WriteLine("OK!");
        Console.Read();
    }
}
//回到顶部
//2.文件操作

//(1)移动、复制、删除文件
class Program
{
    static void Main(string[] args)
    {
        string path = @"E:\学习笔记\C#平台\Test.txt";
        File.WriteAllText(path, "测试数据");
        Console.WriteLine("文件已创建，请查看！");
        Console.ReadLine();
        File.Move(path, @"E:\学习笔记\Test.txt");
        Console.WriteLine("移动完成，请查看！");
        Console.ReadLine();
        File.Copy(@"E:\学习笔记\Test.txt", path);
        Console.WriteLine("文件已复制，请查看！");
        Console.ReadLine();
        File.Delete(path);
        File.Delete(@"E:\学习笔记\Test.txt");
        Console.WriteLine("文件已删除，请查看！\nOK!");
        Console.Read();
    }
}
(2)判断是文件还是文件夹
class Program
{
    static void Main(string[] args)
    {
        IsFile(@"E:\学习笔记\C#平台\Test.txt");
        IsFile(@"E:\学习笔记\");
        IsFile(@"E:\学习笔记\XXXXXXX");
        Console.Read();
    }
    //判断路径是否是文件或文件夹
    static void IsFile(string path)
    {
        if (Directory.Exists(path))
        {
            Console.WriteLine("是文件夹！");
        }
        else if (File.Exists(path))
        {
            Console.WriteLine("是文件！");
        }
        else
        {
            Console.WriteLine("路径不存在！");
        }
    }
}

//3.读写文件与数据流

(1)读文件
class Program
{
    static void Main(string[] args)
    {
        string path = @"E:\学习笔记\C#平台\Test.txt";
        byte[] b = File.ReadAllBytes(path);
        Console.WriteLine("ReadAllBytes读二进制:");
        foreach (byte temp in b)
        {
            Console.Write((char)temp+" ");
        }
        string[] s = File.ReadAllLines(path, Encoding.UTF8);
        Console.WriteLine("\nReadAllLines读所有行:");
        foreach (string temp in s)
        {
            Console.WriteLine("行："+temp);
        }
        string str = File.ReadAllText(path, Encoding.UTF8);
        Console.WriteLine("ReadAllText读所有行:\n" + str);
        Console.Read();
    }
}
(2)写文件
class Program
{
    static void Main(string[] args)
    {
        string path = @"E:\学习笔记\C#平台\Test.txt";
        File.WriteAllBytes(path,new byte[] {0,1,2,3,4,5,6,7,8,9});//写入二进制
        Console.WriteLine("WriteAllBytes写入二进制成功");
        Console.ReadLine();
        string[] array = {"123","456","7890"};
        File.WriteAllLines(path, array, Encoding.UTF8);//写入所有行
        Console.WriteLine("WriteAllLines写入所有行成功");
        Console.ReadLine();
        File.WriteAllText(path, "abcbefghijklmn",Encoding.UTF8);//写入字符串
        Console.WriteLine("WriteAllText写入字符串成功\nOK!");
        Console.Read();
    }
}/**
(3)数据流
    最常用的流类如下：
        FileStream：     文件流，可以读写二进制文件。 
        StreamReader：   流读取器，使其以一种特定的编码从字节流中读取字符。 
        StreamWriter：   流写入器，使其以一种特定的编码向流中写入字符。 
        BufferedStream： 缓冲流，给另一流上的读写操作添加一个缓冲层。
    数据流类的层次结构：
 
(4)使用FileStream读写二进制文件*/
class Program
{
    static void Main(string[] args)
    {
        string path = @"E:\学习笔记\C#平台\Test.txt";
        //以写文件的方式创建文件
        FileStream file = new FileStream(path, FileMode.CreateNew, FileAccess.Write);
        string str = "测试文件--李志伟";
        byte[] bytes = Encoding.Unicode.GetBytes(str);
        file.Write(bytes, 0, bytes.Length);//写入二进制
        file.Dispose();
        Console.WriteLine("写入数据成功！！！");
        Console.ReadLine();
        //以读文件的方式打开文件
        file = new FileStream(path, FileMode.Open, FileAccess.Read);
        byte[] temp = new byte[bytes.Length];
        file.Read(temp, 0, temp.Length);//读取二进制
        Console.WriteLine("读取数据：" + Encoding.Unicode.GetString(temp));
        file.Dispose();
        Console.Read();
    }
}
/*
(5)StreamWriter与StreamReader
    使用StreamWriterStreamReader就不用担心文本文件的编码方式，所以它们很适合读写文本文件。*/
class Program
{
    static void Main(string[] args)
    {
        string path = @"E:\学习笔记\C#平台\Test.txt";
        //以写文件的方式创建文件
        FileStream file = new FileStream(path, FileMode.Create, FileAccess.Write);
        StreamWriter sw = new StreamWriter(file);
        sw.WriteLine("测试文件--李志伟");
        sw.Dispose();
        Console.WriteLine("写入数据成功！！！");
        Console.ReadLine();
        //以读文件的方式打开文件
        file = new FileStream(path, FileMode.Open, FileAccess.Read);
        StreamReader sr = new StreamReader(file);
        Console.WriteLine("读取数据:"+sr.ReadToEnd());
        sr.Dispose();
        Console.Read();
    }
}

/**
4.映射内存的文件

(1)MemoryMappedFile类(.NET4新增)
    应用程序需要频繁地或随机地访问文件时，最好使用MemoryMappedFile类(映射内存的文件)。使用这种方式允许把文件的一部分或者全部加载到一段虚拟内存上，这些文件内容会显示给应用程序，就好像这个文件包含在应用程序的主内存中一样。
    */
//(2)使用示例
class Program
{
    static void Main(string[] args)
    {
        MemoryMappedFile mmfile = MemoryMappedFile.CreateFromFile(@"E:\Test.txt", FileMode.OpenOrCreate, "MapName", 1024 * 1024);
        MemoryMappedViewAccessor view = mmfile.CreateViewAccessor();//内存映射文件的视图
        //或使用数据流操作内存文件
        //MemoryMappedViewStream stream = mmfile.CreateViewStream();
        string str = "测试数据：李志伟!";
        int length = Encoding.UTF8.GetByteCount(str);
        view.WriteArray<byte>(0, Encoding.UTF8.GetBytes(str), 0, length);//写入数据
        byte[] b = new byte[length];
        view.ReadArray<byte>(0, b, 0, b.Length);
        Console.WriteLine(Encoding.UTF8.GetString(b));
        mmfile.Dispose();//释放资源
        Console.Read();
    }
}

/**
5.文件安全

(1)ACL介绍
    ACL是存在于计算机中的一张表(访问控制表)，它使操作系统明白每个用户对特定系统对象，例如文件目录或单个文件的存取权限。每个对象拥有一个在访问控制表中定义的安全属性。这张表对于每个系统用户有拥有一个访问权限。最一般的访问权限包括读文件（包括所有目录中的文件），写一个或多个文件和执行一个文件（如果它是一个可执行文件或者是程序的时候）。
(2)读取文件的ACL*/
class Program
{
    static void Main(string[] args)
    {
        FileStream file = new FileStream(@"E:\Test.txt", FileMode.Open, FileAccess.Read);
        FileSecurity filesec = file.GetAccessControl();//得到文件访问控制属性
        //输出文件的访问控制项
        foreach (FileSystemAccessRule filerule in filesec.GetAccessRules(true, true, typeof(NTAccount)))
        {
            Console.WriteLine(filerule.AccessControlType + "--" + filerule.FileSystemRights + "--" + filerule.IdentityReference);
        }
        file.Dispose();
        Console.Read();
    }
}
//(3)读取文件夹的ACL
class Program
{
    static void Main(string[] args)
    {
        DirectoryInfo dir= new DirectoryInfo(@"E:\学习笔记\C#平台");
        DirectorySecurity filesec = dir.GetAccessControl();//得到文件访问控制属性
        //输出文件的访问控制项
        foreach (FileSystemAccessRule filerule in filesec.GetAccessRules(true, true, typeof(NTAccount)))
        {
            Console.WriteLine(filerule.AccessControlType + "--" + filerule.FileSystemRights + "--" + filerule.IdentityReference);
        }
        Console.Read();
    }
}
//(4)修改ACL
class Program
{
    static void Main(string[] args)
    {
        FileStream file = new FileStream(@"E:\Test.txt", FileMode.Open, FileAccess.Read);
        FileSecurity filesec = file.GetAccessControl();//得到文件访问控制属性
        Print(filesec.GetAccessRules(true, true, typeof(NTAccount)));//输出文件访问控制项
        FileSystemAccessRule rule = new FileSystemAccessRule(
            new NTAccount(@"CENTER-YFB-512\LiZW"), //计算机账户名
            FileSystemRights.Delete, //操作权限
            AccessControlType.Allow);//能否访问受保护的对象
        filesec.AddAccessRule(rule);//增加ACL项
        Print(filesec.GetAccessRules(true, true, typeof(NTAccount)));//输出文件访问控制项
        filesec.RemoveAccessRule(rule);//移除ACL项
        Print(filesec.GetAccessRules(true, true, typeof(NTAccount)));//输出文件访问控制项
        file.Dispose();
        Console.Read();
    }
    //输出文件访问控制项
    static void Print(AuthorizationRuleCollection rules)
    {
        foreach (FileSystemAccessRule filerule in rules)
        {
            Console.WriteLine(filerule.AccessControlType + "--" + filerule.FileSystemRights + "--" + filerule.IdentityReference);
        }
        Console.WriteLine("================================================");
    }
}

/**
6.读写注册表

(1)注册表介绍
    Windows注册表是帮助Windows控制硬件、软件、用户环境和Windows界面的一套数据文件，运行regedit可以看到5个注册表配置单元(实际有7个)：
        HKEY-CLASSES-ROOT：      文件关联和COM信息
        HKEY-CURRENT-USER：      用户轮廓
        HKEY-LOCAL-MACHINE：     本地机器系统全局配置子键
        HKEY-USERS：             已加载用户轮廓子键
        HKEY-CURRENT-CONFIG：    当前硬件配置
(2).NET操作注册表的类
    在.NET中提供了Registry类、RegistryKey类来实现对注册表的操作。其中Registry类封装了注册表的七个基本主健：
        Registry.ClassesRoot    对应于HKEY_CLASSES_ROOT主键 
        Registry.CurrentUser    对应于HKEY_CURRENT_USER主键 
        Registry.LocalMachine   对应于 HKEY_LOCAL_MACHINE主键 
        Registry.User           对应于 HKEY_USER主键 
        Registry.CurrentConfig  对应于HEKY_CURRENT_CONFIG主键 
        Registry.DynDa          对应于HKEY_DYN_DATA主键 
        Registry.PerformanceData 对应于HKEY_PERFORMANCE_DATA主键
    RegistryKey类封装了对注册表的基本操作，包括读取，写入，删除。其中读取的主要函数有：　
        OpenSubKey()        主要是打开指定的子键。 
        GetSubKeyNames()    获得主键下面的所有子键的名称，它的返回值是一个字符串数组。 
        GetValueNames()     获得当前子键中的所有的键名称，它的返回值也是一个字符串数组。 
        GetValue()          指定键的键值。
    写入的函数：
　　    CreateSubKey()  增加一个子键 
　　    SetValue()      设置一个键的键值
    删除的函数：
　　    DeleteSubKey()      删除一个指定的子键。
　　    DeleteSubKeyTree()  删除该子键以及该子键以下的全部子键。
　　    */
//(3)示例
class Program
{
    static void Main(string[] args)
    {
        string path = @"SOFTWARE\Microsoft\Internet Explorer\Extensions";
        RegistryKey pregkey = Registry.LocalMachine.OpenSubKey(path, true);//以只读方式
        if (pregkey != null)
        {
            Console.WriteLine(pregkey.Name + "--" + pregkey.SubKeyCount + "--" + pregkey.ValueCount);
            string preName = System.Guid.NewGuid().ToString();
            pregkey.CreateSubKey(preName);//增加一个子键
            RegistryKey new_pregkey = Registry.LocalMachine.OpenSubKey(path + @"\" + preName, true);
            new_pregkey.SetValue("姓名", "李志伟");//设置一个键的键值
            new_pregkey.SetValue("键名", "值内容");//设置一个键的键值
            Console.WriteLine(pregkey.Name + "--" + pregkey.SubKeyCount + "--" + pregkey.ValueCount);
            pregkey.Close();
            new_pregkey.Close();
        }
        Console.Read();
    }
}
/*
7.读写独立的存储器

(1)IsolatedStorageFile类
    使用IsolatedStorageFile类可以读写独立的存储器，独立的存储器可以看成一个虚拟磁盘，在其中可以保存只由创建他们的应用程序或其应用程序程序实例共享的数据项。
    独立的存储器的访问类型有两种(如下图)：第一种是一个应用程序的多个实例在同一个独立存储器中工作，第二种是一个应用程序的多个实例在各自不同的独立存储器中工作。
*/
//示例
class Program
{
    static void Main(string[] args)
    {
        //写文件
        IsolatedStorageFileStream storStream = new IsolatedStorageFileStream(@"Test.txt", FileMode.Create, FileAccess.Write);
        string str = "测试数据：李志伟！ABCD";
        byte[] bs = Encoding.UTF8.GetBytes(str);
        storStream.Write(bs, 0, bs.Length);//写数据
        storStream.Dispose();
        //读文件
        IsolatedStorageFile storFile = IsolatedStorageFile.GetUserStoreForDomain();
        string[] files=storFile.GetFileNames(@"Test.txt");
        foreach (string t in files)
        {
            Console.WriteLine(t);
            storStream = new IsolatedStorageFileStream(t, FileMode.Open, FileAccess.Read);
            StreamReader sr=new StreamReader(storStream);
            Console.WriteLine("读取文件："+sr.ReadToEnd());
            sr.Dispose();
            storFile.DeleteFile(t);//删除文件
        }
        storFile.Dispose();
        Console.WriteLine("OK!");
        Console.Read();
    }
}
```