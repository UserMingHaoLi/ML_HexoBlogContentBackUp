---
title: CSharp编程指南_特殊字符
date: 2021-04-29 01:15:00
updated: 2021-04-29 01:15:00
id: ml-20210508-225300-g135
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

* $:内插的字符串字符
* @:逐字字符串标识符字符


<!--more-->

# $ - 字符串内插

与使用`字符串复合格式设置`功能创建格式化字符串相比,字符串内插提供的语法更具可读性,且更加方便
```CSharp
Console.WriteLine("Hello, {0}! Today is {1}, it's {2:HH:mm} now.", name, date.DayOfWeek, date);
Console.WriteLine($"Hello, {name}! Today is {date.DayOfWeek}, it's {date:HH:mm} now.");
```

解构示意图

```CSharp
{<interpolationExpression>[,<alignment>][:<formatString>]}
```

| 元素                    | 描述                                                                                                                                                         |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| interpolationExpression | 生成需要设置格式的结果的表达式. null 的字符串表示形式为 String.Empty                                                                                        |
| alignment               | 常数表达式,它的值定义表达式结果的字符串表示形式中的最小字符数. 如果值为正,则字符串表示形式为右对齐；如果值为负,则为左对齐. 有关详细信息,请参阅对齐组件 |
| formatString            | 受表达式结果类型支持的格式字符串. 有关更多信息,请参阅格式字符串组件                                                                                        |

```CSharp
Console.WriteLine($"|{"Left",-7}|{"Right",7}|");
const int FieldWidthRightAligned = 20;
Console.WriteLine($"{Math.PI,FieldWidthRightAligned} - default formatting of the pi number");
Console.WriteLine($"{Math.PI,FieldWidthRightAligned:F3} - display only three decimal digits of the pi number");
// Expected output is:
// |Left   |  Right|
//     3.14159265358979 - default formatting of the pi number
//                3.142 - display only three decimal digits of the pi number
```

## 特殊字符

要在内插字符串生成的文本中包含大括号 "{" 或 "}",请使用两个大括号,即 "{{" 或 "}}"

因为冒号（":"）在内插表达式项中具有特殊含义,为了在内插表达式中使用条件运算符,请将表达式放在括号内

```CSharp
string name = "Horace";
int age = 34;
Console.WriteLine($"He asked, \"Is your name {name}?\", but didn't wait for a reply :-{{");
Console.WriteLine($"{name} is {age} year{(age == 1 ? "" : "s")} old.");
// Expected output is:
// He asked, "Is your name Horace?", but didn't wait for a reply :-{
// Horace is 34 years old.
```

## 隐式转换和指定 IFormatProvider 实现的方式

* 将内插字符串转换为 String 实例,该类例是内插字符串的解析结果,其中内插表达式项被替换为结果的格式设置正确的字符串表示形式. 此转换使用 CurrentCulture 设置表达式结果的格式
* 将内插字符串转换为表示复合格式字符串的 FormattableString 实例,同时也将表达式结果格式化. 这允许通过单个 FormattableString 实例创建多个包含区域性特定内容的结果字符串. 要执行此操作,请调用以下方法之一
  * ToString() 重载,生成 CurrentCulture 的结果字符串.
  * Invariant 方法,生成 InvariantCulture 的结果字符串.
  * ToString(IFormatProvider) 方法,生成特定区域性的结果字符串.
还可以使用 ToString(IFormatProvider) 方法,以提供支持自定义格式设置的 IFormatProvider 接口的用户定义实现. 有关详细信息,请参阅在 .NET 中设置类型格式一文中的使用 ICustomFormatter 进行自定义格式设置部分

* 将内插字符串转换为 IFormattable 实例,使用此实例也可通过单个 IFormattable 实例创建多个包含区域性特定内容的结果字符串

## 内插字符串编译

如果内插字符串类型为 string,则通常将其转换为 String.Format 方法调用

如果分析的行为等同于串联,则编译器可将 String.Format 替换为 String.Concat

如果内插字符串类型为 IFormattable 或 FormattableString,则编译器会生成对 FormattableStringFactory.Create 方法的调用

## 复合格式设置

复合格式设置功能受诸如以下方法的支持

* String.Format,它返回格式化的结果字符串.
* StringBuilder.AppendFormat,它将格式化的结果字符串追加到 StringBuilder 对象.
* Console.WriteLine 方法的某些重载,它将格式化的结果字符串显示到控制台上.
* TextWriter.WriteLine 方法的某些重载,它将格式化的结果字符串写入流或文件中. 派生自 TextWriter 的类（如 StreamWriter 和 HtmlTextWriter）也共享此功能.
* Debug.WriteLine(String, Object[]),它将格式化消息输出到跟踪侦听器.
* Trace.TraceError(String, Object[])、Trace.TraceInformation(String, Object[]) 和 Trace.TraceWarning(String, Object[]) 方法,它们将格式化消息输出到跟踪侦听器.
* TraceSource.TraceInformation(String, Object[]) 方法,它将信息性方法写入跟踪侦听器中.

复合格式字符串和对象列表将用作支持复合格式设置功能的方法的参数. 复合格式字符串由零个或多个固定文本段与一个或多个格式项混和组成

```CSharp
string name = "Fred";
String.Format("Name = {0}, hours = {1:hh}", name, DateTime.Now);
```

### 索引组件

即`{index}`

### 对齐组件

类似于`{0,-20}`或`{1,5:N1}`

使 20 字符字段中的姓名左对齐,使 5 字符字段中的工作小时数右对齐

注意"N1"标准格式字符串还用于设置带有小数位的小时数格式

# 格式字符串组件

* 标准日期和时间格式字符串
* 自定义日期和时间格式字符串
* 枚举格式字符串
* 标准数字格式字符串
* 自定义数字格式字符串
* Guid.ToString(String)
* 标准 TimeSpan 格式字符串
* 自定义 TimeSpan 格式字符串

# @:逐字字符串标识符字符

@ 特殊字符用作原义标识符. 它具有以下用途

* 使 C# 关键字用作标识符, 如`@for`
* 指示将原义解释字符串,将按字面解释,而不是转义结果

```CSharp
string filename1 = @"c:\documents\files\u0066.txt";
string filename2 = "c:\\documents\\files\\u0066.txt";
```
```CSharp
string s1 = "He said, \"This is the last \u0063hance\x0021\"";
string s2 = @"He said, ""This is the last \u0063hance\x0021""";
Console.WriteLine(s1);
Console.WriteLine(s2);
// The example displays the following output:
//     He said, "This is the last chance!"
//     He said, "This is the last \u0063hance\x0021"
```

* 使编译器在命名冲突的情况下区分两种属性


```CSharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class Info : Attribute
{
   private string information;

   public Info(string info)
   {
      information = info;
   }
}

[AttributeUsage(AttributeTargets.Method)]
public class InfoAttribute : Attribute
{
   private string information;

   public InfoAttribute(string info)
   {
      information = info;
   }
}

[Info("A simple executable.")] // "Info"在"Info"和"InfoAttribute"之间不明确；请使用"@Info"或"InfoAttribute"
//[@Info("A simple executable.")] 
public class Example
{
   [InfoAttribute("The entry point.")]
   public static void Main()
   {
   }
}
```

从 C# 8.0 开始,可以按任意顺序使用 $ 和 @ 标记  
之前的版本只能以 $ 字符开头,后跟 @ 字符  

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
