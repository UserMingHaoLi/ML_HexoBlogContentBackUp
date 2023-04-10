---
title: CSharp10.0
date: 2022-03-08 00:04:00
updated: 2022-03-08 00:04:00
id: ml-20220308-000400-g177
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---


- [记录结构](#记录结构)
- [结构类型的改进](#结构类型的改进)
- [内插字符串处理程序](#内插字符串处理程序)
- [全局 using 指令](#全局-using-指令)
- [文件范围的命名空间声明](#文件范围的命名空间声明)
- [扩展属性模式](#扩展属性模式)
- [Lambda 表达式改进](#lambda-表达式改进)
- [常数内插字符串](#常数内插字符串)
- [记录类型可以密封 ToString](#记录类型可以密封-tostring)
- [在同一解构中进行赋值和声明](#在同一解构中进行赋值和声明)
- [改进型明确赋值](#改进型明确赋值)
- [CallerArgumentExpression 属性诊断](#callerargumentexpression-属性诊断)
- [增强型 #line pragma](#增强型-line-pragma)
- [允许在方法上使用 AsyncMethodBuilder 特性](#允许在方法上使用-asyncmethodbuilder-特性)
- [完毕](#完毕)


<!--more-->

# 记录结构

可以使用 `record struct` 或 `readonly record struct` 声明值类型记录  
同样, 也可`record class` 声明引用类型记录

# 结构类型的改进

`C#10` 引入了与结构类型相关的以下改进

* 可以在结构类型中声明实例无参数构造函数,并在声明实例字段或属性时对它们进行初始化
* `with` 表达式的左侧操作数可以是任何结构类型,也可以是匿名（引用）类型

# 内插字符串处理程序

可自定义处理内插字符串

# 全局 using 指令

可将 `global` 修饰符添加到任何 `using` 指令,以指示编译器该指令适用于编译中的所有源文件. 这通常是项目中的所有源文件

# 文件范围的命名空间声明

```CSharp
namespace MyNamespace;
```

就是省略了大括号, 这将导致本文件内都处于此命名空间

# 扩展属性模式

从 `C# 10` 开始,可引用属性模式中嵌套的属性或字段

```CSharp
{ Prop1.Prop2: pattern }
```

在 `C# 10` 及更高版本中有效,且其等效项

```CSharp
{ Prop1: { Prop2: pattern } }
```

# Lambda 表达式改进

```CSharp
var parse = (string s) => int.Parse(s);
```

编译器可以将 `parse` 推断为 `Func<string, int>`

编译器选择可用的 `Func` 或 `Action` 委托（如果存在合适的委托）. 否则,它将合成委托类型

> 不推荐吧, 还是声明类型好点

# 常数内插字符串

从 `C# 10` 开始,可以使用字符串内插来初始化常量字符串

用于占位符的所有表达式都必须是常量字符串. 换言之,每个内插表达式都必须是一个字符串,并且必须是编译时常量.


# 记录类型可以密封 ToString

在 `C# 10` 中,在记录类型中重写 `ToString` 时可以添加 `sealed` 修饰符. 密封 `ToString` 方法可阻止编译器为任何派生的记录类型合成 `ToString` 方法

确保了所有派生记录类型都使用某个通用基记录类型中定义的 `ToString` 方法

# 在同一解构中进行赋值和声明

此更改取消了早期 C# 版本中的限制. 以前,析构可以将所有值赋给现有变量,或将新声明的变量初始化:

```CSharp
// Initialization:
(int x, int y) = point;

// assignment:
int x1 = 0;
int y1 = 0;
(x1, y1) = point;
```

`C# 10` 取消了此限制:
```CSharp
int x = 0;
(x, int y) = point;
```

# 改进型明确赋值

此次改进的主要影响是,针对明确赋值和 Null 状态分析的警告更加准确.



# CallerArgumentExpression 属性诊断

如果条件为 `false`,则异常消息包含传递给 `condition` 的参数的文本表示形式:

```CSharp
public static void Validate(bool condition, [CallerArgumentExpression("condition")] string? message=null)
{
    if (!condition)
    {
        throw new InvalidOperationException($"Argument failed validation: <{message}>");
    }
}

bool bValidateCondition = false;
Validate(bValidateCondition); //Out print `Argument failed validation: <{bValidateCondition}>`
```

> 也就是得知调用方的参数名称

如果传入进来的是一个表达式,那么记录下来的就是表达式本身,比如`5/10 + 2`

# 增强型 #line pragma
# 允许在方法上使用 AsyncMethodBuilder 特性

略

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
