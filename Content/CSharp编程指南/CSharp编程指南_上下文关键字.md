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

# and

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

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
