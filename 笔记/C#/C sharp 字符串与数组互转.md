#### 1、string 转 byte[]
```C
	public static byte[] StringToBytes(string str)
	{
		byte[] byteArry;
		byteArray = System.Text.Encoding.Default.GetBytes(str);
		return byteArry;
	}
```

#### 2、byte转string
```C
	public static BytesToString(byte[] byteArry)
	{
		string str;
		str = System.Text.Encoding.UTF8.GetString(byteArry);
		return str;
	}
```

#### 3、16进制string转bytes
```C
public static HexstringToBytes(string Hexstring)
{
//1、清理空格
	string SplitString;
	SplitString = Clear_string(Hexstring);
		
//2、分割子串
	string[] hexstring = MySplit(SplitString, 2);
		
//3、检擦字串是否符合16进制
	if (!IsHexString(hexstring))
	{
		return null;
	}
		
//4、转化字串位byte数组
	return ToBytesFromHexString(hexstring);
}

        //确定字符子串中的字符满足16进制要求
public static bool IsHexString(string[] target)
{
    const string PATTERN = @"[A-Fa-f0-9]+$";
    foreach (string s in target)
    {
                //判断输入格式是否符合16进制字符串
        if (!System.Text.RegularExpressions.Regex.IsMatch(s, PATTERN))
            {
                MessageBox.Show("请输出正确的16进制数");
                return false;
            }
    }
        return true;
}

        //利用正则表达式清空字符串中的空格
public static string Clear_string(string target)
{
            //去掉字符串的空格
    string result;
    result = Regex.Replace(target, @"\s", "");
           // Console.WriteLine(result);
    return result;
}

        //每2个字符串分割成一个字串
public static string[] MySplit(this string str, int count)
{
	var list = new List<string>();
    int length = (int)Math.Ceiling((double)str.Length / count);

    for (int i = 0; i < length; i++)
    {
        int start = count * i;
        if (str.Length <= start)
        {
            break;
        }
        if (str.Length < start + count)
        {
            list.Add(str.Substring(start));
        }
        else
        {
            list.Add(str.Substring(start, count));
        }
    }
    return list.ToArray();
}

        //将字符字串逐个变为byte数组
public static byte[] ToBytesFromHexString(string[] chars)
{
            //逐个字串转化为Byte数组
    byte[] returnBytes = new byte[chars.Length];
            //逐个字符变为16进制字节数据
    int i = 0;
    foreach (string s in chars)
    {
        returnBytes[i] = Convert.ToByte(s, 16);
        i++;
    }
    return returnBytes;
}
```

#### 4、byte[]转16进制字符串
```C
public static string BytesToHexstring(byte[] byteArry)
{
	string str;
	str = BitConverter.ToString(bytArry).Replace("-", "");
	return str;
}
```

#### 5、 8位16进制字符转byte
```C
public static byte HexstringToByte(string str)
{
	return Convert.ToByte(str,16);
}
```

#### 6、单个byte转16进制字符串
```C
public static string ByteToHexstring(byte _byte)
{
	string result= Convert.ToString(_byte, 16);// result="61"
	return result;
}
  
```

#### 7、普通字符串转16进制字符串
```C
public static string StringToHexstring(string str)
{
	byte[] byteArry;
	byteArray = System.Text.Encoding.Default.GetBytes(str);
	return BitConverter.ToString(byteArry).Replace("-", "");
}
```
