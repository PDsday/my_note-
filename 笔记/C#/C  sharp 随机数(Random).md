		Random为命名空间System下一个类，为伪随机数生成器。能够产生满足某些随机性统计要求的数组序列的设备。
		它的随机性，和Linux的random相近，不算是真的随机。但是就使用角度而言，这种程度已经足够。
		Random继承于Object类。

### Random的属性和方法(成员)

|序号|成员名|描述|
|:---:|:---:|:---:|
|1|Equals|这是一个继承成员，不必关心|
|2|Finalize|这是一个继承成员，不必关心|
|3|GetHashCode|继承成员，哈希函数，不必关心|
|4|GetType|继承成员，返回实例的类型|
|5|MemberwiseClone|继承成员|
|6|Next|自身成员，有2个重载，执行此方法会返回一个随机数|
|7|NextBytes|返回一个用随机数填充只当字节数组的元素|
|8|NextDouble|返回一个介于0和1之间的随机双精度浮点型|
|9|Sample|返回一个介于0和1之间的随机数|
|10|ToString|继承成员，不必关心|

		比较重要的方法为：next 它对应的重载有：
		random.next(),返回一个随机数；
		random.next(num),返回一个不大于num的随机数；
		random.next(num1.num2),返回一个介于num1和num2的随机数；

		NextDouble/Sample没有重载。

### 常见用法，生成不重复的随机数

		有两种方法生成不重复的随机数，两种方法都是将生成的数值存储进链表中，当有新的随机数产生时，会和逐个对比链表的数据，如果没有相同，则新的随机数不是重复随机数。
		这些方法，在内存上的占用不可避免，但是，不同的链表遍历方式能节省相当多的计算时间。其中，哈希链表是最省时的。下面介绍哈希表胜场不重复随机数的代码。

 #使用Hashtable 

```C#
//Hanstable是命名空间 System.Collections中的一个类
//用于生成哈希表

using System.Collection;

Hashtable hashtable = new Hashtable();//生成空的哈希表
Random rm = new Random; //实例化一个随机数

//测试的过程是，将Random生成的随机数，放入哈希表中判断
//如果表中没有这个随机数，则随机数可用，并将随机数记录在哈希表中
/*哈希表的遍历元素的算法优秀，因此当生成大量随机数越多，
比较的数目越多时，遍历速度更快。
*/

int RmNum = 10000;//哈希表的存储个数限制，
for (int i = 0; hashtable.Count < RmNum; i++) 
{ 
	int nValue = rm.Next(20000); //生成20000以内的随机数
	
	if (!hashtable.ContainsValue(nValue)) 
	{ 
		hashtable.Add(nValue, nValue); 
		//Console.Write(nValue + "\t"); 
	}
 }

```

