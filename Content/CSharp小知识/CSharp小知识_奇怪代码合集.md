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
```C#
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

```C#
static void Main(string[] args)
{
	Foo foo = (IFoo) null;
	foo.Name = "lindexi";

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

```C#
class Program
{
	static async Task Main(string[] args)
	{
		Foo foo = await (object) null;
		foo.Name = "lindexi";

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
```C#
await await await await await await await await await await await await
await await await await await await await "String";
```
*只需将返回值设置为`String`即可实现递归*

# 如何不执行 finally 里面的代码

```C#
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
```C#
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

```C#
var n = new int[int.MaxValue];
//提示内存不够，但是finally依然可以运行
//---
Thread.CurrentThread.Abort();
//线程中断异常 和其他异常一样都能执行 finally 代码
```

# 无限级判断空

```C#
var v1 = "123";
string v2 = null;
string v3 = null;
var v = v1 ?? v2 ?? v3;
```

```C#
var n = 2 + foo?.N ?? 1;
```
*为空时如何求值?*

按照优先级转化后如下`(2 + foo?.N) ?? 1`  
如果`foo`为`null`,则前半段不会运行, 等同于如下代码

```C#
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

```C#
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
```C#
a ?? b ?? c
d ??= e ??= f
```
结果
```C#
a ?? (b ?? c)
d ??= (e ??= f)
```

又因为`+`的优先级大于`??`  
所以
```C#
var n = (2 + foo?.N) + (foo2.N ?? 1);//0+1
```

> 如果你的代码中执行函数,那么无论如何,执行所有函数

如`1 + foo?.N + foo2?.N + 2 + foo2?.N  + foo?.N + 1 ?? 1`  
将会执行所有foo2?.N

# 

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
