---
title: CSharp小知识_奇怪代码合集
date: 2021-06-21 02:22:00
updated: 2021-06-21 02:22:00
id: ml-20210621-022200-g145
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---



<!--more-->

# 从死循环中退出
```CSharp
 static void Main(string[] args)
{
	 var thread = new Thread(Foo);
	thread.Start();
	Task.Delay(100).Wait();
	thread.Abort();// 这时就会结束循环

	Console.Read();
}
private static void Foo()
{
	try
	{
		while (true)
		{
		}
	}
	finally
	{
		Console.WriteLine("尝试调用 Foo 函数执行这一句代码");
	}
}
```
*使用线程停止,之后会调用finally*

# 从null强转出一个类

```CSharp
static void Main(string[] args)
{
	Foo foo = (IFoo) null;
	foo.Name = "ML";

	Console.Read();
}
class IFoo
{

}

class Foo
{
	public string Name { get; set; }

	public static implicit operator Foo(IFoo foo)
	{
		return new Foo();
	}
}
```
*重写了强转运算符*

# 等待不存在的类

```CSharp
class Program
{
	static async Task Main(string[] args)
	{
		Foo foo = await (object) null;
		foo.Name = "ML";

		Console.Read();
	}
}

public class Foo
{
	public string Name { get; set; }
}
public class HeabdsdnbKevx : INotifyCompletion
{
	public bool IsCompleted { get; }

	public Foo GetResult()
	{
		return new Foo();
	}

	/// <inheritdoc />
	public void OnCompleted(Action continuation)
	{
	}
}

public static class RelelnisSou 
{
	public static HeabdsdnbKevx GetAwaiter(this object obj)
	{
		return new HeabdsdnbKevx();
	}
}
```
*为要等待的类型编写扩展函数,此函数的返回值需要拥有`await`要求的三个成员.*

如下也是成立的
```CSharp
await await await await await await await await await await await await
await await await await await await await "String";
```
*只需将返回值设置为`String`即可实现递归*

# 如何不执行 finally 里面的代码

```CSharp
try
{
	Foo();
}
finally
{
	Console.WriteLine("不要让这个代码运行");
} 
```

可以使用强制退出,堆栈溢出,不安全代码申请,干掉自己进程
```CSharp
Environment.Exit(0);
//---
private static void Foo()
{
	Foo();
}
//---
unsafe
{
	var n = stackalloc int[int.MaxValue];
}
//---
Process.GetCurrentProcess().Kill();
```
但是直接申请大内存和退出当前线程方法都会让 finally 执行

```CSharp
var n = new int[int.MaxValue];
//提示内存不够，但是finally依然可以运行
//---
Thread.CurrentThread.Abort();
//线程中断异常 和其他异常一样都能执行 finally 代码
```

# 无限级判断空

```CSharp
var v1 = "123";
string v2 = null;
string v3 = null;
var v = v1 ?? v2 ?? v3;
```

```CSharp
var n = 2 + foo?.N ?? 1;
```
*为空时如何求值?*

按照优先级转化后如下`(2 + foo?.N) ?? 1`  
如果`foo`为`null`,则前半段不会运行, 等同于如下代码

```CSharp
if (foo == null)
{
	n = 1;
}
else
{
	n = 2 + foo.N;
}
```

> 实际上还是算了结果,只是被丢弃了

```CSharp
class Foo
{
	public int N
	{
		get
		{
			Console.WriteLine("Hi.");
			return 1;
		}
	}
}

static void Main()
{
	Foo foo = null;
	Foo foo2 = new Foo();
	var n = 2 + foo?.N + foo2.N ?? 1;
	Console.WriteLine(n);
}
```

`null 合并运算符`是右结合运算符。 也就是说，是窗体的表达式
```CSharp
a ?? b ?? c
d ??= e ??= f
```
结果
```CSharp
a ?? (b ?? c)
d ??= (e ??= f)
```

又因为`+`的优先级大于`??`  
所以
```CSharp
var n = (2 + foo?.N) + (foo2.N ?? 1);//0+1
```

> 如果你的代码中执行函数,那么无论如何,执行所有函数

如`1 + foo?.N + foo2?.N + 2 + foo2?.N  + foo?.N + 1 ?? 1`  
将会执行所有foo2?.N

# 模式匹配

```CSharp
class B
{
public static int operator &(B left, B right) => 1;
public static int operator >(B left, B right) => 2;
public static int operator <(B left, B right) => 3;

public static int operator &(bool left, B right) => 5;
public static int operator >(bool left, B right) => 6;
public static int operator <(bool left, B right) => 7;
}

private static B B { get; }

static void Main(string[] args)
{
	object a = null;
	B c = null;
	Console.WriteLine(a is B b & c);
	Console.WriteLine(a is B b1 > c);
	Console.WriteLine(a is B b2 < c);

	a = new B();

	Console.WriteLine(a is B b5 & c);
	Console.WriteLine(a is B b6 > c);
	Console.WriteLine(a is B b7 < c);

}
```
*输出567567*

因为`a is B b`此语句返回bool值, 表示是否转化成功,并在转化成功时,将结果填充到b.

所以匹配的函数为`bool, B`

# 使用 using 关键词省略长的定义

```CSharp
using HvcnrclHnlfk = System.Collections.Generic.Dictionary<System.Collections.Generic.List<System.Collections.Generic.List<string>>,string>;
var foo = new HvcnrclHnlfk();
```
*类似于别名*

# Lambda 嵌套

```CSharp
Func<string,string, EventHandler> foo = (x, y) => (s, e) =>
{
	var button = (Button) s;
	button.Left = x;
	button.Top = y;
};

Button1.Click += foo(0, -1);
```
*这里一个委托返回另一个委托

另外,如下是一个委托加减法
```CSharp
((a + b + c) - (a + c))();
```
此式子结果依赖于`ac`是否等同于`ab`或者`bc`. 如果可以等同,则减法生效, 否则使用左侧式子.

```CSharp
static int nd = 1;
public static void Add(int n)
{
	Console.WriteLine(n * nd);
	nd *= 10;
}

Action<int> a = Add;
Action<int> b = Add;
Action<int> c = Add;
((a + b + c) - (a + c))(5);
Action<int> a = (a) => Add(a);
Action<int> b = (a) => Add(a);
Action<int> c = (a) => Add(a);
((a + b + c) - (a + c))(5);
```

## 委托组合

```CSharp
Action a = () => Console.Write("a");
Action b = () => Console.Write("b");
Action ab = a + b;
ab();  // output: ab
```

## 委托删除

如果两个操作数都为非空，并且右侧操作数的调用列表是左侧操作数调用列表的正确连续子列表，则该操作的结果是通过从左侧操作数的调用列表中删除右侧操作数的条目而获得的新调用列表

果右侧操作数的列表与左侧操作数列表中的多个连续子列表匹配，则仅删除最右侧的匹配子列表

如果删除行为导致出现空列表，则结果为 `null`

```CSharp
Action a = () => Console.Write("a");
Action b = () => Console.Write("b");

var abbaab = a + b + b + a + a + b;
abbaab();  // output: abbaab
Console.WriteLine();

var ab = a + b;
var abba = abbaab - ab;
abba();  // output: abba
Console.WriteLine();

var nihil = abbaab - abbaab;
Console.WriteLine(nihil is null);  // output: True
```

如果右侧操作数的调用列表不是左侧操作数调用列表的正确连续子列表，则该操作的结果是左侧操作数。 例如，删除不属于多播委托的委托不会执行任何操作，从而导致不变的多播委托
```CSharp
Action a = () => Console.Write("a");
Action b = () => Console.Write("b");

var abbaab = a + b + b + a + a + b;
var aba = a + b + a;

var first = abbaab - aba;
first();  // output: abbaab
Console.WriteLine();
Console.WriteLine(object.ReferenceEquals(abbaab, first));  // output: True

Action a2 = () => Console.Write("a");
var changed = aba - a;
changed();  // output: ab
Console.WriteLine();
var unchanged = aba - a2;
unchanged();  // output: aba
Console.WriteLine();
Console.WriteLine(object.ReferenceEquals(aba, unchanged));  // output: True
```

如果左侧操作数为 null，则操作结果为 null。 如果右侧操作数为 null，则操作的结果是左侧操作数
```CSharp
Action a = () => Console.Write("a");

var nothing = null - a;
Console.WriteLine(nothing is null);  // output: True

var first = a - null;
a();  // output: a
Console.WriteLine();
Console.WriteLine(object.ReferenceEquals(first, a));  // output: True
```

# 冲突的类型

一样是使用别名

```CSharp
using web = System.Web.UI.WebControls;
using win = System.Windows.Forms;
web::Control webControl = new web::Control();
win::Control formControl = new win::Control();
```

# extern alias

也是为了解决命名冲突

```CSharp
//a.dll

namespace F
{
	public class Foo
	{

	}
}

//b.dll

namespace F
{
	public class Foo
	{
		
	}
}
```

使用编辑器指令`csc /r:Gva=a.dll /r:Gvb=b.dll mygrid.cs`

```CSharp
extern alias Gva;

extern alias Gvb;

Gva::F.Foo …

Gvb::F.Foo …
```

# 使用 Unions 

这样值公用内存

```CSharp
[StructLayout(LayoutKind.Explicit)]
public class A
{
    [FieldOffset(0)]
    public byte One;

    [FieldOffset(1)]
    public byte Two;

    [FieldOffset(2)]
    public byte Three;

    [FieldOffset(3)]
    public byte Four;

    [FieldOffset(0)]
    public int Int32;
}
static void Main(string[] args)
{
	A a = new A { Int32 = int.MaxValue };

	Console.WriteLine(a.Int32);
	Console.WriteLine("{0:X} {1:X} {2:X} {3:X}", a.One, a.Two, a.Three, a.Four);

	a.Four = 0;
	a.Three = 0;
	Console.WriteLine(a.Int32);
}
/*
2147483647
FF FF FF 7F
65535
*/
```

# 接口默认方法

C#支持在接口中使用默认方法,以便更新接口,不需要使用者强制实现.

# stackalloc

不安全代码，从栈申请空间

```CSharp
int* block = stackalloc int[100]; 
```

# 指定编译

```CSharp
[Conditional("DEBUG")]
public void F2()
{
	Console.WriteLine("F2");
}
```
*如果此函数不生效,则与此函数连续运算的式子也都不生效, 如` foo.F1().F2();`, `F1()`不执行*

# true 判断

可以重写`true`和`falase`

```CSharp
public static bool operator true(Foo mt)
{
	return mt._count > 0;
}

public static bool operator false(Foo mt)
{
	return mt._count < 0;
}
```

# 重写运算返回

重写`==`可以更改返回类型,不一定要返回bool

```CSharp
public static string operator !=(Foo f1, Foo f2)
{
	return "";
}
```

# 变量名使用中文

实际上支持所有`Unicode 字符`
```CSharp
public string H\u00e5rføner()
{
	return "可以编译";
}
```

# 表达式树获取函数命名

表达式树功能很强大

```CSharp
static void Main(string[] args)
{
	GetMethodName<Foo>(foo => foo.KzcSevfio());
}

private static void GetMethodName<T>(Expression<Action<T>> action) where T : class
{
	if (action.Body is MethodCallExpression expression)
	{
		Console.WriteLine(expression.Method.Name);
	}
}
```

# DebuggerDisplay

重写`ToString`可以让你获得更多调试信息,但有时候`ToString`已用作别处,所以需要`DebuggerDisplay`

```CSharp
[DebuggerDisplay("{DebuggerDisplay}")]
public sealed class Foo
{
	public int Count { get; set; }

	private string DebuggerDisplay => $"(count {Count})";
}
```

# 数字格式

实际上这些是微软定义的一些格式,你自己也可以通过接口实现这些.

```CSharp
string format = "000;-#;(0)";

string pos = 1.ToString(format);     // 001
string neg = (-1).ToString(format);  // -1
string zer = 0.ToString(format);     // (0)
```

# 调用堆栈

```CSharp
var stackTrace = new StackTrace();
var n = stackTrace.FrameCount;
for (int i = 0; i < n; i++)
{
	Console.WriteLine(stackTrace.GetFrame(i).GetMethod().Name);
}
```

# 在 try 和 finally 抛异常会发生什么

```CSharp
try
{
	throw new ArgumentException("ML is doubi");
}
finally
{
	throw new FileNotFoundException("lsj is doubi");
}
```
*在 finally 抛出的 FileNotFoundException 将会替换掉 ArgumentException 抛给了 外界*

# 如果在构造函数抛出异常 析构函数是否会执行

```CSharp
class Foo
{
	public Foo()
	{
		throw new Exception("ML is doubi");
	}

	~Foo()
	{
	}
}
```
*可以进入*  
*.NET 运行时，是先创建出对象，然后再调用对象的构造函数。而在创建出对象时，此对象就需要被加入垃圾回收，加入垃圾回收，自然就会调用到析构函数*

因为构造函数也不一定是一句话都没有跑的, .Net会在前面跑很多其他内容.

# 泛基

```CSharp
public abstract class MyBase<T> where T :MyBase <T>
{

	public static string DataForThisType {get;set;}
	public static T Instance{get;protected set;}
	//每次调用时反射取当前类的所有Members
	public static readonly IReadOnlyDictionary<string,MemberInfo> Members
		=typeof(T)
			.GetMembers()
			.ToDictionary(x=>x.Name);

}



public class MyClass:MyBase<MyClass>
{

	static MyClass()
	{ 
		DataForThisType =string.Format(
			"MyClass got {0} members",Members.Count);
		Instance = new MyClass();
	}

}
```
*让父类可以静态化地知道当前子类*  
方法是在父类定义的,但是子类

希望在父类规定一些行为，让子类无法修改  
实现是依赖一个子类才能获取的值  
不可能知道所有的子类  
不知道子类具体类型的情况下，让父类利用泛型参数先替未来的子类做一些事情

# 左值条件运算符（C# 7）

```CSharp
int a = 2, b = 3, c = default, d = default;
(a > b ? ref c : ref d) = 1;
```
*如此便完成有条件的左侧赋值*  
此式子中`d=1`;

# 复合空合赋值运算符 ??= （C# 8）

```CSharp
a ??= b
```
*等同于 `a = a ?? b`*
```CSharp
if (a is null)
{
    a = b;
}
```

# true switch 表达式（C# 8）

```CSharp
int b = 36;
int a = true switch
{
    _ when b < 0 => 1,
    _ when b < 10 => 2,
    _ when b < 30 => 3,
    _ when b < 60 => 4,
    _ => 5
};
```
*为a赋值*

# 传递的弃元（C# 7）

```CSharp
int a = 3, b;
b = _ = _ = _ = _ = _ = a;
```
*a和b都为3*

# __arglist 关键字兼容早期编程语言的可变长参数序列

```CSharp
[DllImport(...)]
static int printf(string format, __arglist);
```
*大多数时候用于接受外界Dll函数*

# 扩展方法还可以引用传递

```CSharp
static void Flip(this ref bool @this)
{
    @this = !@this;
}
```
*直接改写值类型*

# string a1 = a + "";

怎样都不会报错...

# Internal Interface

自己用但不给别人用的接口

```CSharp
internal interface IInternalSomething : ISomething
{
    string GetDebugString();
}
public class A : IInternalSomething 
{
    public void DoSomething() { }
    string IInternalSomething.GetDebugString() { return "A"; }
}
```
*也就是显示实现接口,然后接口类型`internal`*

# 一个异步流

```CSharp
作者：「已注销」
链接：https://www.zhihu.com/question/27421302/answer/99752969
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

private static BufferBlock<User> userFlow = new BufferBlock<User>();

static void Main(string[] args)
{
	Task.Factory.StartNew(async () => await NewData());
	Task.Factory.StartNew(ProcessData);
	ReadLine();
}


private static async Task NewData()
{
	for (int index = 1; index < 10001; index++)
	{
		userFlow.Post(new User { Name = "张三丰", Number = index });
	}

	await userFlow.Completion;
}

private static void ProcessData()
{
	while (userFlow.Completion.Status != TaskStatus.RanToCompletion)
	{
		User _receiveData = userFlow.Receive();
		_receiveData.IsProcess = (0 == _receiveData.Number % 2);
		WriteLine($"Number : {_receiveData.Number } Name : {_receiveData.Name}  Process : {_receiveData.IsProcess} Process Time : { DateTime.Now }");
	}
}
```

# GC.GetAllocatedBytesForCurrentThread

```CSharp
var beforeAllocatedBytesForCurrentThread = GC.GetAllocatedBytesForCurrentThread();
var invocationList = action.GetInvocationList();
var afterAllocatedBytesForCurrentThread = GC.GetAllocatedBytesForCurrentThread();
Console.WriteLine(afterAllocatedBytesForCurrentThread - beforeAllocatedBytesForCurrentThread);
```



# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
