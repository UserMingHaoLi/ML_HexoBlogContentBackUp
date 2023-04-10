---
title: CSharp8.0
date: 2021-04-05 21:59:00
updated: 2021-04-05 21:59:00
id: ml-20210405-215900-g113
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

部分内容可能需要`.Net Core`提供支持.  

> `Unity 2020.2` 支持 `C#8` 2020.2将支持除默认接口方法外所有C# 8的新功能特性与改进。  
> `Unity 2018.3` 支持 `C#7.3`

- [Readonly 成员](#readonly-成员)
- [默认接口方法](#默认接口方法)
- [模式匹配增强功能(在更多位置中使用更多模式)](#模式匹配增强功能在更多位置中使用更多模式)
  - [Switch表达式](#switch表达式)
  - [属性模式](#属性模式)
  - [元组模式](#元组模式)
  - [位置模式](#位置模式)
  - [Using 声明](#using-声明)
- [静态本地函数](#静态本地函数)
- [可处置的 ref 结构](#可处置的-ref-结构)
- [可为空引用类型](#可为空引用类型)
- [类型为 Null 性](#类型为-null-性)
- [可为空上下文](#可为空上下文)
- [异步流](#异步流)
- [异步可释放](#异步可释放)
- [索引和范围](#索引和范围)
- [Null 合并赋值](#null-合并赋值)
- [非托管构造类型](#非托管构造类型)
- [嵌套表达式中的 stackalloc](#嵌套表达式中的-stackalloc)
- [内插逐字字符串的增强功能](#内插逐字字符串的增强功能)
- [完毕](#完毕)

<!--more-->

# Readonly 成员

可以在`struct`成员中使用`readonly`关键字指定该成员不改变.

也可以用于方法,这指示该方法不改变任何外部参数

```CSharp
public struct Point
{
    public double X { get; set; }
    public double Y { get; set; }
    public readonly double Distance => Math.Sqrt(X * X + Y * Y);

    public readonly override string ToString() =>
        $"({X}, {Y}) is {Distance} from the origin";
}
```

# 默认接口方法

可以为接口提供默认的实现.~~这不是又变成继承了吗?~~

官方文档说`这是一项突破性的变革！`...  
*我觉得可能确实有很多软件需要依靠这项功能来提供更新,但是尽量还是少用的好.*

# 模式匹配增强功能(在更多位置中使用更多模式)

匹配模式之前有讲.

## Switch表达式

先看代码

```CSharp
public static RGBColor FromRainbow(Rainbow colorBand) =>
    colorBand switch
    {
        Rainbow.Red    => new RGBColor(0xFF, 0x00, 0x00),
        Rainbow.Orange => new RGBColor(0xFF, 0x7F, 0x00),
        Rainbow.Yellow => new RGBColor(0xFF, 0xFF, 0x00),
        Rainbow.Green  => new RGBColor(0x00, 0xFF, 0x00),
        Rainbow.Blue   => new RGBColor(0x00, 0x00, 0xFF),
        Rainbow.Indigo => new RGBColor(0x4B, 0x00, 0x82),
        Rainbow.Violet => new RGBColor(0x94, 0x00, 0xD3),
        _              => throw new ArgumentException(message: "invalid enum value", paramName: nameof(colorBand)),
    };
```

将变量置于switch关键字前, 这便于区分语句和表达式.  
将case和:替换为 => 更简洁,也更明了  
default被替换为 _ 有弃元的意义.  
正文是表达式而不是语句.  
没有break.

和他等效的代码是

```CSharp
public static RGBColor FromRainbowClassic(Rainbow colorBand)
{
    switch (colorBand)
    {
        case Rainbow.Red:
            return new RGBColor(0xFF, 0x00, 0x00);
        case Rainbow.Orange:
            return new RGBColor(0xFF, 0x7F, 0x00);
        case Rainbow.Yellow:
            return new RGBColor(0xFF, 0xFF, 0x00);
        case Rainbow.Green:
            return new RGBColor(0x00, 0xFF, 0x00);
        case Rainbow.Blue:
            return new RGBColor(0x00, 0x00, 0xFF);
        case Rainbow.Indigo:
            return new RGBColor(0x4B, 0x00, 0x82);
        case Rainbow.Violet:
            return new RGBColor(0x94, 0x00, 0xD3);
        default:
            throw new ArgumentException(message: "invalid enum value", paramName: nameof(colorBand));
    };
}
```

> 说白了就是个语法糖

## 属性模式

先看代码

```CSharp
public static decimal ComputeSalesTax(Address location, decimal salePrice) =>
    location switch
    {
        { State: "WA" } => salePrice * 0.06M,
        { State: "MN" } => salePrice * 0.75M,
        { State: "MI" } => salePrice * 0.05M,
        // other cases removed for brevity...
        _ => 0M
    };
```

实际上就是上面代码的变种, 将常量变为变量形态,  
语法上加入了 `{}` 中间是 `{变量 : 值}`, 类似于switch, 符合的值会执行后面的语句.  
最后也是 `_` 收尾.

## 元组模式

看代码

```CSharp
public static string RockPaperScissors(string first, string second)
    => (first, second) switch
    {
        ("rock", "paper") => "rock is covered by paper. Paper wins.",
        ("rock", "scissors") => "rock breaks scissors. Rock wins.",
        ("paper", "rock") => "paper covers rock. Paper wins.",
        ("paper", "scissors") => "paper is cut by scissors. Scissors wins.",
        ("scissors", "rock") => "scissors is broken by rock. Rock wins.",
        ("scissors", "paper") => "scissors cuts paper. Scissors wins.",
        (_, _) => "tie"
    };
```

这个其实是第一个 `Switch表达式` 的元组形态扩展. 看看就懂了.  
> 本来就觉得很复杂了, 还来个元组,直接就是指数级了...


## 位置模式

实际上就是通过`Deconstruct`解构函数来将所有类型转化为元组  
*注意`point`是存在解构函数的*
```CSharp
static Quadrant GetQuadrant(Point point) => point switch
{
    (0, 0) => Quadrant.Origin,
    var (x, y) when x > 0 && y > 0 => Quadrant.One,
    var (x, y) when x < 0 && y > 0 => Quadrant.Two,
    var (x, y) when x < 0 && y < 0 => Quadrant.Three,
    var (x, y) when x > 0 && y < 0 => Quadrant.Four,
    var (_, _) => Quadrant.OnBorder,
    _ => Quadrant.Unknown
};
```
> 所以如果是非默认值(这里是`0`或`_`),则需要声明一个变量来装解构的元组

## Using 声明

```CSharp
static void WriteLinesToFile(IEnumerable<string> lines)
{
    using var file = new System.IO.StreamWriter("WriteLines2.txt");
    foreach (string line in lines)
    {
        // If the line doesn't contain the word 'Second', write the line to the file.
        if (!line.Contains("Second"))
        {
            file.WriteLine(line);
        }
    }
// file is disposed here
}
```

在声明需要清理的资源时, 可以前置一个using关键字, 这样到作用域的末尾, 就会自动释放.
> 实际上就是省略了一个大括号.  
> 所谓的释放实际是调用`Dispose()`,如果没有,会报错.

# 静态本地函数

```CSharp
int M()
{
    int y = 5;
    int x = 7;
    return Add(x, y);

    static int Add(int left, int right) => left + right;
}
```

使用static修饰本地函数, 使其不会访问任何外界变量, 不会产生*闭包*.  
闭包问题很严重, 和上面结构的`Readonly`一样,用于处理函数闭包

# 可处置的 ref 结构

用 `ref` 修饰符声明的 `struct` 可能无法实现任何接口,因此无法实现 `IDisposable。` 因此,要能够处理 `ref struct`,它必须有一个可访问的 `void Dispose()` 方法。 这同样适用于 `readonly ref struct` 声明。

也就是说`using`使用名字查找而不是`interface`

# 可为空引用类型

这里还有一个隐藏的概念,就是存在一个`不可为空引用类型`

这个是为了修复之前为数据库设计的可空类型的bug.

```CSharp
string? name;   //可为空的string, 所以没有`?`就是不可为空
```

```CSharp
name!.Length;   //强制调用
```

# 类型为 Null 性

任何引用类型都可以具有四个"为 Null 性"中的一个,它描述了何时生成警告：

* 不可为空：无法将 null 分配给此类型的变量。 在取消引用之前,无需对此类型的变量进行 null 检查。
*  可为空：可将 null 分配给此类型的变量。 在不首先检查 null 的情况下取消引用此类型的变量时发出警告。
* 无视："无视"是 C# 8.0 之前版本的状态。 可以取消引用或分配此类型的变量而不发出警告。
* 未知："未知"通常针对类型参数,其中约束不告知编译器类型是否必须是"可为 null"或"不可为 null" 。

变量声明中类型的为 Null 性由声明变量的"可为空上下文"控制。

# 可为空上下文

C# 8.0 之前的编译器视为 *任何引用类型都可以为空*

可以使用 .csproj 文件中的 Nullable 元素为项目设置可为空注释上下文和可为空警告上下文。 此元素配置编译器如何解释类型的为 Null 性以及生成哪些警告。 有效设置如下：

* enable："启用"可为空注释上下文。 "启用"可为空警告上下文。
引用类型的变量,例如 string 是"不可为空"。 启用所有为 Null 性警告。
* warnings："禁用"可为空注释上下文。 "启用"可为空警告上下文。
引用类型的变量是"无视"。 启用所有为 Null 性警告。
* annotations："启用"可为空注释上下文。 "禁用"可为空警告上下文。
引用类型的变量（例如字符串）不可为 null。 禁用所有为 Null 性警告。
* disable："禁用"可为空注释上下文。 "禁用"可为空警告上下文。
引用类型的变量是"无视",就像早期版本的 C# 一样。 禁用所有为 Null 性警告。

```XML
<Nullable>enable</Nullable>
```

还可以使用指令在项目的任何位置设置这些相同的上下文：
* #nullable enable：将可为空注释上下文和可为空警告上下文设置为"已启用"。
* #nullable disable：将可为空注释上下文和可为空警告上下文设置为"已禁用"。
* #nullable restore：将可为空注释上下文和可为空警告上下文还原到项目设置。
* #nullable disable warnings：将可为空警告上下文设置为"已禁用"。
* #nullable enable warnings：将可为空警告上下文设置为"已启用"。
* #nullable restore warnings：将可为空警告上下文还原到项目设置。
* #nullable disable annotations：将可为空注释上下文设置为"禁用"。
* #nullable enable annotations：将可为空注释上下文设置为"启用"。
* #nullable restore annotations：将注释警告上下文还原到项目设置。

> `null` 确实很让人头疼,这次总算可以让全局没有null了.

# 异步流

* 它是用 `async` 修饰符声明的。  
* 它将返回 `IAsyncEnumerable<T>`  
* 该方法包含用于在异步流中返回连续元素的 `yield return` 语句。  

```CSharp
//声明
public static async System.Collections.Generic.IAsyncEnumerable<int> GenerateSequence()
{
    for (int i = 0; i < 20; i++)
    {
        await Task.Delay(100);
        yield return i;
    }
}
//使用
await foreach (var number in GenerateSequence())
{
    Console.WriteLine(number);
}
```

就是以异步的方式使用`yield return`.  
> 看起来像是一个异步迭代器.

# 异步可释放

`System.IAsyncDisposable` 接口 声明异步可释放类型  
 可使用 `await using` 语句调用

# 索引和范围

新增一个索引运算符 `^`

```CSharp
var words = new string[]
{
                // index from start    index from end
    "The",      // 0                   ^9
    "quick",    // 1                   ^8
    "brown",    // 2                   ^7
    "fox",      // 3                   ^6
    "jumped",   // 4                   ^5
    "over",     // 5                   ^4
    "the",      // 6                   ^3
    "lazy",     // 7                   ^2
    "dog"       // 8                   ^1
};
//使用
words[^1] //倒数第一个
//注意不能使用^0, 这会越界
//等同于 worlds[words.Length - 1]
```

新增了`..`索引运算符, 他表示范围  
[1..3]表示从1到2. 不含3;

words[^2..^0];  
它包括 words[^2] 和 words[^1], 不包括结束索引 words[^0]  
> 所以包含前项,不包含后项 [2,0)

此外可以将范围声明为变量：  Range phrase = 1..4;  

然后可以在 [ 和 ] 字符中使用该范围： var text = words[phrase];

*实际上是一个新的类型`System.Index`在支持这种语法*

此外可以将范围声明为变量
```CSharp
Range phrase = 1..4;
```

# Null 合并赋值

`??=`  
仅当左操作数计算为 `null` 时,才能使用运算符 `??=` 将其右操作数的值分配给左操作数。

```CSharp
List<int> numbers = null;
int? i = null;  //?表示i可为空,所以这里给了个null

numbers ??= new List<int>();//如果numbers为null,则将右侧赋给它
numbers.Add(i ??= 17);//如果i为null则将17赋给它
numbers.Add(i ??= 20);//20没有赋值成功,所以还是17

Console.WriteLine(string.Join(" ", numbers));  // output: 17 17
Console.WriteLine(i);  // output: 17
```

# 非托管构造类型

现在可以为`struct`创建非托管类型.

# 嵌套表达式中的 stackalloc

略, 好像是和内存相关的,需要`unsafe`

# 内插逐字字符串的增强功能

内插逐字字符串中 `$` 和 `@` 标记的顺序可以任意安排：`$@"..."` 和 `@$"..."` 均为有效的内插逐字字符串。 在早期 C# 版本中,`$` 标记必须出现在 `@` 标记之前。

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
