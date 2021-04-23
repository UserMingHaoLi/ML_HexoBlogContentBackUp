---
title: CSharp编程指南_命名空间关键字
date: 2021-04-23 22:06:00
updated: 2021-04-23 22:06:00
id: ml-20210423-220600-g128
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

 `C# 7.0` 开始，`is` 和 `switch` 语句支持模式匹配。 
 
 is 关键字支持以下模式

* **类型模式**，用于测试表达式是否可转换为指定类型，如果可以，则将其转换为该类型的一个变量。
* **常量模式**，用于测试表达式计算结果是否为指定的常数值。
* **var 模式**，始终成功的匹配，可将表达式的值绑定到新局部变量。

<!--more-->

# 类型模式

`is` 会测试表达式是否可转换为指定类型，如果可以，则将其转换为该类型的一个变量

```C#
expr is type varname
```

`expr` 是计算结果为某个类型的实例的表达式  
`type` 是 `expr` 结果要转换到的类型的名称  
`varname` 是 `expr` 结果要转换到的对象（如果 `is` 测试为 `true`）

如果 `expr` 不为 `null` 且以下任意条件为 `true`，那么 `is` 表达式为 `true`

* `expr` 是与 `type` 具有相同类型的一个实例。
* `expr` 是派生自 `type` 的类型的一个实例。 换言之，`expr` 结果可以向上转换为 `type` 的一个实例。
* `expr` 具有属于 `type` 的一个基类的编译时类型，`expr` 还具有属于 `type` 或派生自 `type` 的运行时类型。 变量的编译时类型是其声明中定义的变量类型。 变量的运行时类型是分配给该变量的实例类型。
* `expr` 是实现 `type` 接口的类型的一个实例。

> 自 `C# 7.1` 起，`expr` 可能有泛型类型参数及其约束定义的编译时类型

如果 `expr` 为 `true` 且 `is` 与 `if` 语句配合使用，则仅在 `if` 语句内分配 `varname。` `varname` 的使用范围：从 `is` 表达式到封闭 `if` 语句的块的末尾

演示
```C#
public class Employee : IComparable
{
    public String Name { get; set; }
    public int Id { get; set; }

    public int CompareTo(Object o)
    {
        if (o is Employee e)
        {
            return Name.CompareTo(e.Name);
        }
        throw new ArgumentException("o is not an Employee object.");
    }
}
```

演示2
```C#
if (o is Person p) 
{
	Console.WriteLine(p.Name);
}
else if (o is Dog d) 
{
	Console.WriteLine(d.Breed);
}
```

对于无模式匹配的等效代码，需要为其单独分配显式转换

```C#
if (o is Person) 
{
	Person p = (Person) o;
	Console.WriteLine(p.Name);
}
else if (o is Dog) 
{
	Dog d = (Dog) o;
	Console.WriteLine(d.Breed);
}
```

# 常量模式

使用常量模式执行模式匹配时，`is` 会测试表达式结果是否等于指定常量

> 在 `C# 6` 和更低版本中，`switch` 语句支持常量模式。 自 `C# 7.0` 起，`is` 语句也支持

```C#
const int HIGH_ROLL = 6;
if(Roll() is HIGH_ROLL)
{

}
```
*可以看到,就是上面的常量变种.*

可以使用常量模式执行 `null` 检查

```C#
 if (o is null)
```

对于引用类型和`可以为 null 的值类型`，表达式 `x is null` 的计算方式有所不同。，它使用 `Nullable<T>.HasValue`。 对于引用类型，它使用 `(object)x == null`

# var模式

具有 `var` 模式的模式匹配始终成功

```C#
Factor(n).ToList() is var factors
```

`expr` 的值始终分配给名为 `varname` 的局部变量 。 `varname` 变量的类型与 `expr` 的编译时类型相同 

如果 `expr` 的计算结果为 `null`，则 `is` 表达式将生成 `true` 并将 `null` 分配给 `varname`

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
