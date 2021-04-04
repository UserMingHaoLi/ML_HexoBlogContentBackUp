---
title: CSharp6.0
date: 2021-04-04 22:10:00
updated: 2021-04-04 22:10:00
id: ml-20210404-221000-g111
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

- [C#6.0](#c60)
	- [静态导入](#静态导入)
	- [异常筛选器](#异常筛选器)
	- [自动属性初始化表达式](#自动属性初始化表达式)
	- [Expression bodied 成员](#expression-bodied-成员)
	- [Null 传播器](#null-传播器)
	- [字符串内插](#字符串内插)
	- [nameof 运算符](#nameof-运算符)
	- [索引初始值设定项](#索引初始值设定项)
	- [Catch/Finally 块中的 Await](#catchfinally-块中的-await)
	- [仅限 getter 属性的默认值](#仅限-getter-属性的默认值)
	- [Roslyn 编译器即服务](#roslyn-编译器即服务)
- [完毕](#完毕)


<!--more-->

[toc]

# C#6.0

## 静态导入

`using static System.Math;`

使用`using static`显示声明引用静态命名


* 导入的成员签名和现有的成员签名相同时，使用现有的成员。
* 导入的成员之间出现成员签名相同的情况，使用的时候会编译不通过，需要移除一个using static才可，或者改为正常的调用方式。
* class,struct,emun类型可以使用using static导人。
* 扩展方法也可以使用using static，但是需要按照实例方法的调用方式来使用。


## 异常筛选器

允许你为`catch`增加一个逻辑判断, 只有为`true`才能捕获异常  
用于判断`catch`是否有能力处理异常

```C#
catch (System.Net.Http.HttpRequestException e) when (e.Message.Contains("301"))
{
    return "Site Moved";
}
```

使用`when`关键字指定一个`bool`表达式, 或者是返回`bool`的方法

## 自动属性初始化表达式

允许你声明属性的时候就为他们赋值

```C#
public ICollection<double> Grades { get; } = new List<double>();
```

实际上, 我们都知道, 这次初始化是被移动到了构造函数中的.

## Expression bodied 成员

你编写的许多成员是可以作为单个表达式的单个语句。 改为编写 `expression-bodied` 成员。 这适用于方法和只读属性。 例如，重写 `ToString()` 通常是理想之选

```C#
public override string ToString() => $"{LastName}, {FirstName}";

public string FullName => $"{FirstName} {LastName}";
```

很明显, 是以类似`lambda`的语法为指定方法赋值.

## Null 传播器

使用`?.`来代替`.`就不用进行烦人的`null == person`判断了

```C#
var first = person?.FirstName; 
```

还可以将 null 条件运算符用于数组或索引器访问。 将索引表达式中的 [] 替换为 ?[]\

此时如果`person`为null, 返回null, 而不是引发空引用异常.

## 字符串内插

使用`$`开头声明一个字符串. 其中的`{}`将被认为进行字符串内插.

```C#
$"{FirstName} {LastName}"
```

`{}`内的代码将会和外界的普通代码一样运作. 得出一个结果

```C#
${Grades.Average()}
```

上面代码将计算一个平均值.并转化为字符串.

## nameof 运算符

`nameof` 表达式的计算结果为符号的名称。 他是一个关键字 每当需要变量、属性或成员字段的名称时，这是让工具正常运行的好办法。`nameof` 的其中一个最常见的用途是提供引起异常的符号的名称：

```C#
if (IsNullOrWhiteSpace(lastName))
    throw new ArgumentException(message: "Cannot be blank", paramName: nameof(lastName));
```

## 索引初始值设定项

```C#
private Dictionary<int, string> messages = new Dictionary<int, string>
{
    { 404, "Page not Found"},
    { 302, "Page moved, but left a forwarding address."},
    { 500, "The web server can't come out to play today."}
};

private Dictionary<int, string> webErrors = new Dictionary<int, string>
{
    [404] = "Page not Found",
    [302] = "Page moved, but left a forwarding address.",
    [500] = "The web server can't come out to play today."
};
```

类似于集合初始化访问器, 不过这次是字典.

## Catch/Finally 块中的 Await

 使用 C# 6，现在可以在 catch 或 finally 表达式中使用 await。 这通常用于日志记录方案：
 
 ```C#
 catch (System.Net.Http.HttpRequestException e) when (e.Message.Contains("301"))
    {
        await logError("Recovered from redirect", e);
        return "Site Moved";
    }
    finally
    {
        await logMethodExit();
        client.Dispose();
    }
 ```

## 仅限 getter 属性的默认值

现在可以使用简写声明一个仅`get`的属性

```C#
public string FirstName { get; }
```

## Roslyn 编译器即服务

用C#编写的C#编译器, 现在你可以用C#编译C#了, [github][link1]

[link1]: https://github.com/dotnet/roslyn "roslyn"

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
