---
title: CSharp编程指南_上下文关键字
date: 2021-04-25 22:18:00
updated: 2021-04-25 22:18:00
id: ml-20210425-221800-g132
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

上下文关键字用于在代码中提供特定含义，但它不是 C# 中的保留字  
只在特定上下文中用作关键字.

| 关键字  | 说明                                                                |
| ------- | ------------------------------------------------------------------- |
| add     | 定义一个自定义事件访问器，客户端代码订阅事件时会调用该访问器。      |
| and     | 创建在两个嵌套模式均匹配时匹配的模式。                              |
| async   | 指示修改后的方法、lambda 表达式或匿名方法是异步的。                 |
| await   | 挂起异步方法，直到等待的任务完成。                                  |
| dynamic | 定义一个引用类型，实现发生绕过编译时类型检查的操作。                |
| get     | 为属性或索引器定义访问器方法。                                      |
| global  | 未以其他方式命名的全局命名空间的别名。                              |
| init    | 为属性或索引器定义访问器方法。                                      |
| nint    | 定义本机大小的整数数据类型。                                        |
| not     | 创建在否定模式不匹配时匹配的模式。                                  |
| nuint   | 定义本机大小的无符号整数数据类型。                                  |
| or      | 创建在任一嵌套模式匹配时匹配的模式。                                |
| partial | 在整个同一编译单元内定义分部类、结构和接口。                        |
| record  | 用于定义记录类型。                                                  |
| remove  | 定义一个自定义事件访问器，客户端代码取消订阅事件时会调用该访问器。 |
| set     | 为属性或索引器定义访问器方法。                                      |
| value   | 用于设置访问器和添加或删除事件处理程序。                            |
| var     | 使编译器能够确定在方法作用域中声明的变量类型。                      |
| when    | 指定 catch 块的筛选条件或 switch 语句的 case 标签。                 |
| where   | 将约束添加到泛型声明。 （另请参阅 where）。                         |
| yield   | 在迭代器块中使用，用于向枚举数对象返回值或用于表示迭代结束。        |

<!--more-->

# add

# remove

`add` 上下文关键字用于定义一个在客户端代码订阅你的事件时调用的自定义事件访问器。 如果提供自定义 `add` 访问器，还必须提供 `remove` 访问器
```C#
class Events : IDrawingObject
{
    event EventHandler PreDrawEvent;

    event EventHandler IDrawingObject.OnDraw
    {
        add => PreDrawEvent += value;
        remove => PreDrawEvent -= value;
    }//向事件中添加或者删除触发
}
```
大多数情况下，使用声明事件时由编译器自动生成的访问器就足够了

# not

# and

# or

 `C# 9.0` 开始，可使用 `not`、`and` 和 `or` 模式连结符来创建以下逻辑模式

否定 `not` 模式在否定模式与表达式不匹配时与表达式匹配
```C#
if (input is not null)
{
    // ...
}
```

合取 `and` 模式在两个模式都与表达式匹配时与表达式匹配
```C#
static string Classify(double measurement) => measurement switch
{
    < -40.0 => "Too low",
    >= -40.0 and < 0 => "Low",
    >= 0 and < 10.0 => "Acceptable",
    >= 10.0 and < 20.0 => "High",
    >= 20.0 => "Too high",
    double.NaN => "Unknown",
};
```

析取 `or` 模式在任一模式与表达式匹配时与表达式匹配
```C#
static string GetCalendarSeason(DateTime date) => date.Month switch
{
    3 or 4 or 5 => "spring",
    6 or 7 or 8 => "summer",
    9 or 10 or 11 => "autumn",
    12 or 1 or 2 => "winter",
    _ => throw new ArgumentOutOfRangeException(nameof(date), $"Date with unexpected month: {date.Month}."),
};
```
*可在模式中重复使用模式连结符*

`and` 模式连结符的优先级高于 `or`  
如果混用,还是建议使用括号.

> 检查模式的顺序是不确定的。 在运行时，可能先检查 `or` 和 `and` 模式的右侧嵌套模式。

从 `C# 8.0` 开始，可使用属性模式来将表达式的属性或字段与嵌套模式进行匹配

```C#
static bool IsConferenceDay(DateTime date) => date is { Year: 2020, Month: 5, Day: 19 or 20 or 21 };
```

从 `C# 8.0` 开始，可使用位置模式来解构表达式结果并将结果值与相应的嵌套模式匹配
```C#
public readonly struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y) => (X, Y) = (x, y);

    public void Deconstruct(out int x, out int y) => (x, y) = (X, Y);
}

static string Classify(Point point) => point switch
{
    (0, 0) => "Origin",
    (1, 0) => "positive X basis end",
    (0, 1) => "positive Y basis end",
    _ => "Just a point",
};
```

结合前面的两种用法，如以下示例所示
```C#
public record WeightedPoint(int X, int Y)
{
    public double Weight { get; set; }
}

static bool IsInDomain(WeightedPoint point) => point is (>= 0, >= 0) { Weight: >= 0.0 };

if (input is WeightedPoint (> 0, > 0) { Weight: > 0.0 } p)
{
    // ..
}
```

# async

# await

略,详见微软文档.

# dynamic

这玩意看不出是个上下文类型,好像啥地方都可以用.

就是不受编译器检测的动态运行时类型.

# get

# set

```C#
class TimePeriod
{
     private double _seconds;

     public double Seconds
     {
         get { return _seconds; }
         set { _seconds = value; }
     }
}
```

# global

`global` 别名，该别名是全局命名空间别名  
与 `::` 限定符一起使用时，`global` 别名始终引用全局命名空间，即使存在用户定义的 `global` 命名空间别名也是如此

```C#
namespace MyCompany.MyProduct.System
{
    class Program
    {
        static void Main() => global::System.Console.WriteLine("Using global alias");//如果不适用`global`.那么会访问`MyCompany.MyProduct.System`
    }

    class Console
    {
        string Suggestion => "Consider renaming this class";
    }
}
```

# init

在 `C# 9` 及更高版本中，`init` 关键字在属性或索引器中定义访问器方法。 `init-only` 资源库仅在对象构造期间为属性或索引器元素赋值. 相当于只读.

```C#
class InitExample
{
     private double _seconds;

     public double Seconds
     {
         get { return _seconds; }
         init { _seconds = value; }
     }
}
```
也可简写
```C#
class InitExampleAutoProperty
{
    public double Hours { get; init; }
}
```

# nint

# unint

实际就是`IntPtr`和`UIntPtr`

# partial

用于指定分布类和分布方法.  
允许在两个不同文件中书写同一个类或方法.

```C#
namespace PC
{
    partial class A
    {
        int num = 0;
        void MethodA() { }
        partial void MethodC();
    }
}
namespace PC
{
    partial class A
    {
        void MethodB() { }
        partial void MethodC() { }
    }
}
```
在处理大型项目或自动生成的代码（如 `Windows` 窗体设计器提供的代码）时，在多个文件间拆分类、结构或接口类型可能会非常有用

# record

从 `C# 9` 开始，可以使用 `record` 关键字定义一个引用类型，用来提供用于封装数据的内置功能  
称作`记录`

如下可以创建具有不可变属性的记录类型
```C#
public record Person(string FirstName, string LastName);
```
```C#
public record Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
};
```

还可以创建具有可变属性和字段的记录类型
```C#
public record Person
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
};
```

虽然记录可以是可变的，但它们主要用于支持不可变的数据模型

* 用于创建具有不可变属性的引用类型的简明语法
* 内置行为对于以数据为中心的引用类型非常有用：
  * 值相等性
  * 非破坏性变化的简明语法
  * 用于显示的内置格式设置
* 支持继承层次结构

这些类型提供值相等性，并且很少或没有任何行为。 但对于相对较大的数据模型，结构类型有一些缺点

* 不支持继承
* 确定值相等性时效率较低
* 某些情况下，会占用更多内存，因为每个实例都有所有数据的完整副本

> 具体内容参阅微软文档.

# value

上下文关键字 `value` 在属性和索引器声明的 `set` 访问器中使用  

此关键字类似于方法的输入参数。 关键字 value 引用客户端代码尝试分配给属性或索引器的值

```C#
public virtual int Number
{
	get { return num; }
	set { num = value; }
}
```

# var

隐式类型本地变量为强类型，就像用户已经自行声明该类型，但编译器决定类型一样

```C#
var i = 10; // Implicitly typed.
int i = 10; // Explicitly typed.
```
*以上代码其实是等同的*

# when

可以使用上下文关键字 when 在以下上下文中指定筛选条件

* 在 `try/catch` 或 `try/catch/finally` 块的 `catch` 语句中。
* 在 `switch` 语句的 `case` 标签中。
* 在 `switch` 表达式中。

```C#
catch (ExceptionType [e]) when (expr)
```
其中，`expr` 是一个表达式，其计算结果为布尔值。 如果该表达式返回 `true`，则执行异常处理程序；如果返回 `false`，则不执行

```C#
case (expr) when (when-condition):
```
仅当筛选条件`when-condition`也为 `true` 时，与其相关联的 `case` 标签才为 `true`

# where

`where` 子句指定对用作泛型参数的约束

```C#
public class AGenericClass<T> where T : IComparable<T> { }
```
*此声明约束`T`实现`IComparable<T>`接口*

更多请看文档.

# yield

使用 `yield` 定义迭代器，可在实现自定义集合类型的 `IEnumerator<T>` 和 `IEnumerable` 模式时无需其他显式类
```C#
yield return <expression>;
yield break;
```

其中`yield break`类似于其他函数的`return`. 表示方法终止.  
而 `yield return` 语句则是一次返回一个元素

可通过使用 `foreach` 语句或 `LINQ` 查询来使用从迭代器方法返回的序列。 `foreach` 循环的每次迭代都会调用迭代器方法  
迭代器方法运行到 `yield return` 语句时,会返回一个 `expression`  
并保留当前在代码中的位置  
次调用迭代器函数时，将从该位置重新开始执行

```C#
IEnumerable<string> elements = MyIteratorMethod();
foreach (string element in elements)
{
   ...
}
```

调用 `MyIteratorMethod` 并不执行该方法的主体  
该调用会将 `IEnumerable<string>` 返回到 `elements`  
在 `foreach` 循环迭代时，将为 `MoveNext` 调用 `elements` 方法  
 此调用将执行 `MyIteratorMethod` 的主体，直至到达下一个 `yield return` 语句

> 这玩意产生CG.

# 查询语句

> `C# 3.0` 中引入的所有查询关键字也都是上下文相关的

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
