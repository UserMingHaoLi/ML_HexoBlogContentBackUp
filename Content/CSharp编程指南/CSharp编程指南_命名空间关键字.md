---
title: CSharp编程指南_命名空间关键字
date: 2021-04-23 21:49:00
updated: 2021-04-23 21:49:00
id: ml-20210423-214900-g127
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

`namespace` 关键字用于声明包含一组相关对象的作用域,称为`命名空间`

无论是否在 `C#` 源文件中显式声明命名空间,编译器都会添加一个默认命名空间

命名空间隐式具有公共访问权限,这是不可修改的

<!--more-->

# using

`using` 关键字有三个主要用途

* `using` 语句定义一个范围,在此范围的末尾将释放对象。
* `using` 指令为命名空间创建别名,或导入在其他命名空间中定义的类型。
* `using static` 指令导入单个类的成员。

# using指令

允许在命名空间中使用类型,这样无需在该命名空间中限定某个类型的使用

```CSharp
using System;
```

允许访问类型的静态成员和嵌套类型,而无需限定使用类型名称进行访问

```CSharp
using static System.Math;
```

> `using static` 指令是在 `C# 6` 中引入的

为命名空间或类型创建别名。 这称为 `using 别名指令`

```CSharp
using Project = PC.MyCompany.Project;
```

# using 语句

提供可确保正确使用 IDisposable 对象的方便语法

>  从 `C#8.0` 开始,`using` 语句可确保正确使用 `IAsyncDisposable` 对象

```CSharp
using (var reader = new StringReader(manyLines))
{
    string? item;
    do {
        item = reader.ReadLine();
        Console.WriteLine(item);
    } while(item != null);
}
```

所有此类类型都必须实现 `IDisposable` 接口或 `IAsyncDisposable` 接口

如果对象实现 `IAsyncDisposable` 而不是 `IDisposable`,`using` 语句将调用 `DisposeAsync` 和 `awaits` 返回的 `ValueTask`

*`using` 语句可确保调用 `Dispose` 或 `DisposeAsync`,即使 `using` 块中发生异常也是如此*

通过将对象放入 `try` 块中,然后调用 `finally` 块中的 `Dispose`（或 `DisposeAsync`）,可以实现相同的结果

这就是编译器转换 `using` 语句的方式  
前面的代码示例在编译时将扩展到以下代码

```CSharp
var reader = new StringReader(manyLines);
try {
	string? item;
	do {
		item = reader.ReadLine();
		Console.WriteLine(item);
	} while(item != null);
} finally
{
	reader?.Dispose();
}
```

# 外部别名

有时你可能不得不引用具有相同的完全限定类型名称的程序集的两个版本  
通过使用外部程序集别名,可在别名命名的根级别命名空间内包装每个程序集的命名空间,使其能够在同一文件中使用

> `extern`关键字还被用作方法修饰符,用于声明在非托管代码中编写的方法。

命令行工具
```shell
/r:GridV1=grid.dll

/r:GridV2=grid20.dll
```

```CSharp
extern alias GridV1;

extern alias GridV2;
```

每个外部别名声明都会引入与全局命名空间并行（但不位于其中）的额外根级别命名空间。 因此,可以使用其完全限定的名称（根植于相应的命名空间别名中）无歧义地引用每个程序集的类型

如果你使用的是 `Visual Studio`,可以按类似方式提供别名。
在 `Visual Studio` 中向项目添加 `grid.dll` 和 `grid20.dll` 的引用。 打开"属性"选项卡,并将别名从"全局"分别更改为"`GridV1`"和"`GridV2`"。

之后随意使用即可
```CSharp
using Class1V1 = GridV1::Namespace.Class1;

using Class1V2 = GridV2::Namespace.Class1;
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
