---
title: CSharp编程指南_编译器读取的属性
date: 2021-05-09 22:45:00
updated: 2021-05-09 22:45:00
id: ml-20210509-224500-g136
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---


# 保留的特性：程序集级别特性

大多数特性应用于特定语言元素，如类或方法；但是，一些特性是全局特性 - 它们应用于整个程序集或模块

<!--more-->

## 程序集标识特性

三个特性（与强名称（如果适用））组合起来可以确定程序集的标识：名称、版本和区域性。

| 特性                     | 目标                                                                     |
| ------------------------ | ------------------------------------------------------------------------ |
| AssemblyVersionAttribute | 指定程序集的版本。                                                       |
| AssemblyCultureAttribute | 指定程序集支持的区域性。                                                 |
| AssemblyFlagsAttribute   | 指定程序集是否支持在同一计算机上、同一进程中或同一应用程序域中并行执行。 |

## 信息性特性

你可使用信息性特性为程序集提供其他公司或产品信息

来自 `System.Reflection` 命名空间中定义的信息性属性

| 特性                                  | 目标                                   |
| ------------------------------------- | -------------------------------------- |
| AssemblyProductAttribute              | 指定程序集清单的产品名称。             |
| AssemblyTrademarkAttribute            | 指定程序集清单的商标。                 |
| AssemblyInformationalVersionAttribute | 为程序集清单指定信息性版本。           |
| AssemblyCompanyAttribute              | 为程序集清单指定公司名称。             |
| AssemblyCopyrightAttribute            | 定义为程序集清单指定版权的自定义属性。 |
| AssemblyFileVersionAttribute          | 设置 Win32 文件版本资源的特定版本号。  |
| CLSCompliantAttribute                 | 指示程序集是否符合公共语言规范 (CLS)。 |

## 程序集清单特性

程序集清单特性可用于提供程序集清单中的信息。 特性包括标题、说明、默认别名和配置。 下表显示 `System.Reflection` 命名空间中定义的程序集清单属性  

| 特性                           | 目标                                         |
| ------------------------------ | -------------------------------------------- |
| AssemblyTitleAttribute         | 为程序集清单指定程序集标题。                 |
| AssemblyDescriptionAttribute   | 为程序集清单指定程序集说明。                 |
| AssemblyConfigurationAttribute | 为程序集清单指定程序集配置（如零售或调试）。 |
| AssemblyDefaultAliasAttribute  | 定义程序集清单的友好默认别名                 |

# 保留的特性：确定调用方信息

使用信息属性，可以获取有关方法调用方的信息

可以获取源代码的文件路径、源代码中的行号和调用方的成员名称

下表列出在 System.Runtime.CompilerServices 命名空间中定义的调用方信息特性：

| 特性                      | 描述                                                    | 类型    |
| ------------------------- | ------------------------------------------------------- | ------- |
| CallerFilePathAttribute   | 包含调用方的源文件的完整路径。 完整路径是编译时的路径。 | String  |
| CallerLineNumberAttribute | 源文件中调用方法的行号。                                | Integer |
| CallerMemberNameAttribute | 调用方的方法名称或属性名称。                            | String  |


```CSharp
public void DoProcessing()
{
    TraceMessage("Something happened.");
}

public void TraceMessage(string message,
        [CallerMemberName] string memberName = "",
        [CallerFilePath] string sourceFilePath = "",
        [CallerLineNumber] int sourceLineNumber = 0)
{
    Trace.WriteLine("message: " + message);
    Trace.WriteLine("member name: " + memberName);
    Trace.WriteLine("source file path: " + sourceFilePath);
    Trace.WriteLine("source line number: " + sourceLineNumber);
}

// Sample Output:
//  message: Something happened.
//  member name: DoProcessing
//  source file path: c:\Visual Studio Projects\CallerInfoCS\CallerInfoCS\Form1.cs
//  source line number: 31
```

# 保留的特性有助于编译器的 null 状态静态分析

在可为 null 的上下文中，编译器对代码执行静态分析，以确定所有引用类型变量的 null 状态

可使用以下属性之一来表示这些规则：

* AllowNull：不可为 null 的参数可以为 null。
* DisallowNull：可为 null 的参数不应为 null。
* MaybeNull：不可为 null 的返回值可以为 null。
* NotNull：可为 null 的返回值永远不会为 null。
* MaybeNullWhen：当方法返回指定的 bool 值时，不可为 null 的参数可以为 null。
* NotNullWhen：当方法返回指定的 bool 值时，可以为 null 的参数不会为 null。
* NotNullIfNotNull：如果指定参数的参数不为 null，则返回值不为 null。
* DoesNotReturn：方法从不返回。 换句话说，它总是引发异常。
* DoesNotReturnIf：如果关联的 bool 参数具有指定值，则此方法永远不会返回。
* MemberNotNull：当方法返回时，列出的成员不会为 null。
* MemberNotNullWhen：当方法返回指定的 bool 值时，列出的成员不会为 null。

> 把我看懵了, 这个应该是可为Null的上下文中准备的....

# 保留属性：其他

## Conditional 特性

Conditional 应用于启用或禁用显示特定于程序的诊断信息的方法

```CSharp
[Conditional("TRACE_ON")]
public static void Msg(string msg)
{
   Console.WriteLine(msg);
}
```
如果未定义 `TRACE_ON` 标识符，则不会显示跟踪输出

`Conditional` 特性通常与 `DEBUG` 标识符一起使用，以启用调试生成（而非发布生成）中的跟踪和日志记录功能

```CSharp
[Conditional("DEBUG")]
static void DebugMethod()
{
}
```

与将方法封闭在 `#if…#endif` 块内相比，`Conditional` 更简洁且较不容易出错

如果某个方法具有多个 `Conditional` 特性，则如果定义了一个或多个条件符号（通过使用 `OR` 运算符将这些符号逻辑链接在一起），则包含对该方法的调用。 在以下示例中，存在 A 或 B 将导致方法调用

```CSharp
[Conditional("A"), Conditional("B")]
static void DoIfAorB()
{
    // ...
}
```

## 使用带有特性类的 Conditional

`Conditional` 特性还可应用于特性类定义。 在以下示例中，如果定义了 `DEBUG`，则自定义特性 `Documentation` 将仅向元数据添加信息

```CSharp
[Conditional("DEBUG")]
public class DocumentationAttribute : System.Attribute
{
    string text;

    public DocumentationAttribute(string text)
    {
        this.text = text;
    }
}

class SampleClass
{
    // This attribute will only be included if DEBUG is defined.
    [Documentation("This method displays an integer.")]
    static void DoWork(int i)
    {
        System.Console.WriteLine(i.ToString());
    }
}
```

## Obsolete 特性

`Obsolete` 特性将代码元素标记为不再推荐使用

```CSharp
using System;

namespace AttributeExamples
{
    [Obsolete("use class B")]
    public class A
    {
        public void Method() { }
    }

    public class B
    {
        [Obsolete("use NewMethod", true)]
        public void OldMethod() { }

        public void NewMethod() { }
    }

    public static class ObsoleteProgram
    {
        public static void Main()
        {
            // Generates 2 warnings:
            A a = new A();

            // Generate no errors or warnings:
            B b = new B();
            b.NewMethod();

            // Generates an error, compilation fails.
            // b.OldMethod();
        }
    }
}
```
* 因为应用于 `B.OldMethod` 的特性构造函数的第二个参数设置为 `true`，所以此方法将导致编译器错误，而使用类 A 只会生成警告*

调用 `B.NewMethod` 不会生成任何警告或错误

*作为特性构造函数的第一个参数提供的字符串将作为警告或错误的一部分显示*

## AttributeUsage 特性

`AttributeUsage` 特性确定自定义特性类的使用方式

* 可能应用到的具体程序元素特性。 除非使用限制，否则特性可能应用到以下任意程序元素：
   * 程序集 (assembly)
   * name
   * field
   * event
   * method
   * param
   * property
   * return
   * type
* 某特性是否可多次应用于单个程序元素。
* 特性是否由派生类继承。

```CSharp
[AttributeUsage(AttributeTargets.All,
                   AllowMultiple = false,
                   Inherited = true)]
class NewAttribute : Attribute { }
```
`NewAttribute` 类可应用于任何受支持的程序元素。 但是它对每个实体仅能应用一次。 特性应用于基类时，它由派生类继承

`AllowMultiple` 和 `Inherited` 参数是可选的，因此以下代码具有相同效果：

```CSharp
[AttributeUsage(AttributeTargets.All)]
class NewAttribute : Attribute { }
```

可将多个目标类型与 OR 运算符链接在一起，如下例所示：

```CSharp
[AttributeUsage(AttributeTargets.Property | AttributeTargets.Field)]
class NewPropertyOrFieldAttribute : Attribute { }
```

## ModuleInitializer 特性

从 `C# 9` 开始，`ModuleInitializer` 属性标记程序集加载时运行时调用的方法

`Init1` 和 `Init2` 方法在 `Main` 之前运行，并且每种方法都将一个字符串添加到 `Text` 属性。 因此，当 `Main` 运行时，`Text` 属性已具有来自两个初始化表达式方法中的字符串

```CSharp
using System.Runtime.CompilerServices;

internal class ModuleInitializerExampleModule
{
    public static string? Text { get; set; }

    [ModuleInitializer]
    public static void Init1()
    {
        Text += "Hello from Init1! ";
    }

    [ModuleInitializer]
    public static void Init2()
    {
        Text += "Hello from Init2! ";
    }
}
```

## SkipLocalsInit 特性

从 `C# 9` 开始，`SkipLocalsInit` 属性可防止编译器在发出到元数据时设置 `.locals init` 标志

在某些情况下，额外的零初始化可能会对性能产生显著影响

在这些情况下，可添加 `SkipLocalsInit` 属性


下面的示例阐释 `SkipLocalsInit` 属性对使用 `stackalloc` 的方法的影响
```
[SkipLocalsInit]
static void ReadUninitializedMemory()
{
    Span<int> numbers = stackalloc int[120];
    for (int i = 0; i < 120; i++)
    {
        Console.WriteLine(numbers[i]);
    }
}
// output depends on initial contents of memory, for example:
//0
//0
//0
//168
//0
//-1271631451
//32767
//38
//0
//0
//0
//38
// Remaining rows omitted for brevity.
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
