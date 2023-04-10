---
title: CSharp编程指南_编译器读取的属性
date: 2021-05-10 22:29:00
updated: 2021-05-10 22:29:00
id: ml-20210510-222900-g137
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

尽管编译器没有单独的预处理器,但本节中所述指令的处理方式与有预处理器时一样。 可使用这些指令来帮助条件编译。 不同于 C 和 C++ 指令,不能使用这些指令来创建宏。 预处理器指令必须是一行中唯一的说明



<!--more-->

# 可为空上下文

每个上下文要么处于已禁用状态,要么处于已启用状态

指令的效果如下所示：
* #nullable disable：将可为空注释和警告上下文设置为"已禁用"。
* #nullable enable：将可为空注释和警告上下文设置为"已启用"。
* #nullable restore：将可为空注释和警告上下文还原为项目设置。
* #nullable disable annotations：将可为空注释上下文设置为"已禁用"。
* #nullable enable annotations：将可为空注释上下文设置为"已启用"。
* #nullable restore annotations：将可为空注释上下文还原为项目设置。
* #nullable disable warnings：将可为空警告上下文设置为"已禁用"。
* #nullable enable warnings：将可为空警告上下文设置为"已启用"。
* #nullable restore warnings：将可为空警告上下文还原为项目设置。

# 条件编译

使用四个预处理器指令来控制条件编译：
* #if：打开条件编译,其中仅在定义了指定的符号时才会编译代码。
* #elif：关闭前面的条件编译,并基于是否定义了指定的符号打开一个新的条件编译。
* #else：关闭前面的条件编译,如果没有定义前面指定的符号,打开一个新的条件编译。
* #endif：关闭前面的条件编译。

```CSharp
#if DEBUG
    Console.WriteLine("Debug version");
#endif
```
```CSharp
#define VC7
//...
#if debug
    Console.WriteLine("Debug build");
#elif VC7
    Console.WriteLine("Visual Studio 7");
#endif
```

# 定义符号

使用以下两个预处理器指令来定义或取消定义条件编译的符号：
* #define：定义符号。
* #undef：取消定义符号。

```CSharp
#define VERBOSE

#if VERBOSE
   Console.WriteLine("Verbose output version");
#endif
```

符号可用于指定编译的条件。 可通过 `#if` 或 `#elif` 测试符号。 还可以使用 `ConditionalAttribute` 来执行条件编译

# 定义区域

可以使用以下两个预处理器指令来定义可在大纲中折叠的代码区域：
* #region：启动区域。
* #endregion：结束区域。

# 错误和警告信息

使用以下指令指示编译器生成用户定义的编译器错误和警告,并控制行信息：
* #error：使用指定的消息生成编译器错误。
* #warning：使用指定的消息生成编译器警告。
* #line：更改用编译器消息输出的行号。

#error 可从代码中的特定位置生成 CS1029 用户定义的错误。 例如

```CSharp
#error Deprecated code in this method.
```

**借助 #line,可修改编译器的行号及（可选）用于错误和警告的文件名输出**

`#line 200` 指令将下一行的行号强制设为 200（尽管默认值为 #6）；在执行下一个 `#line` 指令前,文件名都会报告为"特殊"。 `#line default` 指令将行号恢复至默认行号,这会对上一指令重新编号的行进行计数。

```CSharp
class MainClass
{
    static void Main()
    {
#line 200 "Special"
        int i;
        int j;
#line default
        char c;
        float f;
#line hidden // numbering not affected
        string s;
        double d;
    }
}
```

#line hidden 指令能对调试程序隐藏连续行

#line filename 指令可指定要在编译器输出中显示的文件名

```CSharp
// preprocessor_linehidden.cs
using System;
class MainClass
{
    static void Main()
    {
        Console.WriteLine("Normal line #1."); // Set break point here.
#line hidden
        Console.WriteLine("Hidden line.");
#line default
        Console.WriteLine("Normal line #2.");
    }
}
```

# 杂注

#pragma 为编译器给出特殊指令以编译它所在的文件。 这些指令必须受编译器支持。 换句话说,不能使用 #pragma 创建自定义的预处理指令

* #pragma warning：启用或禁用警告。
* #pragma checksum：生成校验和。

```CSharp
#pragma pragma-name pragma-arguments
```

其中 pragma-name 是可识别 pragma 的名称,pragma-arguments 是特定于 pragma 的参数

一般来说, 编译器会有提示, 是否关闭某个警告

# 完毕



**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
