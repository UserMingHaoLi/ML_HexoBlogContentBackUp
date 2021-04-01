---
title: CSharp3.0
date: 2021-03-31 23:42:00
updated: 2021-03-31 23:42:00
id: ml-20210331-234200-g108
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---


> 这是一次里程碑式的重大改变, 注意了!


<!--more-->

# 自动实现的属性

```C#
int Myint { get; set;}
```

以更简单的方式生成一条属性, 其背后的字段交给编辑器,  
这种属性由于没有编写代码, 默认实现和一下代码相当

```
int myint;
int Myint 
{ 
    get
    {
        return myint;
    }
    set
    {
        myint = value
    }
}
```

但是极大的减少了工作量,  
你可能在想, 既然这样我为什么不直接用字段呢?  
面向对象的基本原理中第一条就是封装, 只有更好的准备,才可以更好的面对问题  
包装一层属性, 未来就可以随时扩展成完全体, 添加一些逻辑方法.  
总比满工程找个这个字段修改来的痛快.  
多写几个字符, 未来节省几小时甚至数天的时间.

# 匿名类型

匿名类型和js中的objcet有些相似.

```
var v = new { Amount = 108, Message = "Hello" }; 
```

由于并不清楚什么类型, 只能是用`var`来接收, 生成对象一定要使用`new`

实际项目中, 我们经常会临时的少量使用一个类, 如果有匿名类型, 那就不用写一个完整的类了.

> 匿名类型只有一次机会获得它, 就是声明的时候使用var接收.  
匿名类型不可以修改其内部的值.  
匿名类型包含一个或多个公共只读属性。包含其他种类的类成员（如方法或事件）为无效。  
用来初始化属性的表达式不能为 null、匿名函数或指针类型。

编辑器为我们创建一个隐形的类.  
再CLI看来, 它和我们自己写的完全体类是一样的.

# 查询表达式

也就是大名鼎鼎的`Linq`, 看本段之前, 先看完后面的其他所有.

查询表达式实际上是很多扩展方法组成的用于表述数据源组织形态的一系列方法.  
微软还为他做了些关键字, 让他看起来像是SQL一样.  

从应用角度看, 无论数据源是什么, 从哪里来, 只要他们格式一样, 都被视为` IEnumerable<T>` 或 `IQueryable<T>` 

看一个Linq

```csharp
IEnumerable<int> highScoresQuery =
    from score in scores
    where score > 80
    orderby score descending
    select score;
```

这个是关键字版本的, 像极了SQL  
首先`scores`是一个分数合集, 使用`from`关键字, 类似于`foreach`拆分`item`
然后是`where`条件筛选, 类似于`if`针对的是每个`score`  
`orderby` 排序 依据`score` 倒序.  
`select` 返回一个新的集合, item为`score`  
此时可以使用匿名类型. 做返回值.

如果是用任何实现`IEnumerable`的类, 都可以看到扩展方法.  
这些方法返回也都是`IEnumerable`于是可以连续的跟随方法.

> 注意, 除了返回实际值的方法, 任何返回`IEnumerable`的`Linq`都是延时执行的, 只在真正使用的时候执行.

# Lambda 表达式

使用简短语句创建一个匿名函数, 和之前的方法组结合, 自动匹配对应代理.
```C#
Action<string> action = a => Console.WriteLine(a);
```

以上从`action =`之后的部分都是`Lambda`  
他由三部分组成  
`()`这是参数部分, 有几个参数就填几个, 没有就不填,  
没有参数一定要保留括号, 只有一个参数连小括号也不用.  
用于在之后的方法体内调用, 注意,由于自动推断的原因, 此处的参数不必指定类型.  
`=>`表示这是一个`Lambda` 类似于关键字吧.  
`{}`代码块区域, 如果代码只有一行, 可以省略花括号和`return`  
如果把上面代码写完整就是这样的.

```C#
Action<string> action = (string a) => { return Console.WriteLine(a) }
```

当然, 由于有花括号, 你可以写任意多行.  
应用场景就是用于赋值给委托, 也可以是事件.  
所有参数需要委托或者事件的地方, 都可以用`Lambda`.  
因为是委托, 这里的`a`类型已经确定了, 就是`string`  
但是具体数据还要等该委托被使用才知道.  
`lambda`的本质还是方法. 是匿名方法, 而且进行了极大幅度的简写,方便使用.

```C#
static void Main()  
{  
  ShowValue(x => x * x);  
}  

private static void ShowValue(Func<int,int> op)
{
    for (int ctr = 1; ctr <= 5; ctr++)
    {
        Console.WriteLine("{0} x {0} = {1}", ctr,  op(ctr));
    }
}
```

# 表达式树

表达式树其实就是上面说的那个`Action<string> action = a => Console.WriteLine(a);`  
因为`lambda`表达式只包含了左半部分, 右半部分的声明和中间的赋值, 与他一起组合成为一个表达式树.  
也就是匿名函数赋值.

当然 , 还可以使用`Expression`类来动态拼凑表达式.  

参阅[表达式树][link1]

# 扩展方法

一个语法糖, 将你自己写的一个静态方法放置到已经完成的类中.  
比如, 我想要一个 `double.TOInt()`这么一个方法, 实际是没有的.  
这时候, 就可以使用扩展方法, 使自己写的方法像原生方法一样使用.

```C#
static class sc
{
    public static int ToInt(this double thas)
    {
        return Convert.ToInt32(thas);
    }
}
double d = 1;
d.ToInt();
```

你只能在静态类中编写一个静态方法, 在第一个参数上使用`this`关键字来指定你要附加到哪一个类中.  
这样, 我们就能像使用`double`原生方法一样使用自己的方法了.

> Linq就是扩展方法的大合集.

> 对于CIL来说, 你仍然是调用的静态方法. 编辑器的障眼法而已.  
与接口或类方法具有相同名称和签名的扩展方法永远不会被调用

# 隐式类型本地变量

`var`, 这就是一个偷懒写法, 如果编辑器可以推断类型, 你为什么要声明他呢.

```
var i = 10;
int i = 10;
```

如果无法推断, 还得手写.

# 分部方法

分布方法可以让你在一处定义方法, 在另一处实现.  
如果你不实现, 编译器在编译时删除该方法.

```
partial class A
{
    partial void OnSomethingHappened(string s);
}

// This part can be in a separate file.
partial class A
{
    // Comment out this method and the program
    // will still compile.
    partial void OnSomethingHappened(String s)
    {
        Console.WriteLine("Something happened: {0}", s);
    }
}
```

# 对象和集合初始值设定项

实际是`{}`

你可以在声明对象和集合的时候直接使用`{}`来进行初始化.  
你可以在初始化的时候访问任何可以访问的字段和属性.  
甚至可以忽略构造方法, 默认调用空构造.

```C#
Cat cat = new Cat { Age = 10, Name = "Fluffy" };
Cat sameCat = new Cat("Fluffy"){ Age = 10 };
```

从 C# 6 开始，除了分配字段和属性外，对象初始值设定项还可以设置索引器。  
使用`[]`声明索引.

```C#
var identity = new Matrix
{
    [0, 0] = 1.0,
    [0, 1] = 0.0,
    [0, 2] = 0.0,

    [1, 0] = 0.0,
    [1, 1] = 1.0,
    [1, 2] = 0.0,

    [2, 0] = 0.0,
    [2, 1] = 0.0,
    [2, 2] = 1.0,
};
```

[link1]: https://docs.microsoft.com/zh-cn/dotnet/csharp/expression-trees "表达式树"

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
