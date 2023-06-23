---
title: CSharp5.0
date: 2021-04-03 22:19:00
updated: 2021-04-03 22:19:00
id: ml-20210401-002800-g110
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

- [C#5.0](#c50)
  - [异步成员](#异步成员)
  - [调用方信息特性](#调用方信息特性)
- [完毕](#完毕)

<!--more-->

# C#5.0


## 异步成员

`await`和`async`关键字, 这是`.Net`的核心功能

`async`是访问修饰符, 表示这是一个异步方法.

`await`是运算关键字, 用于调用异步方法.

使用`await`访问`async`则会停止等待直到执行完毕.

```
public class Example
{
   public static void Main()
   {
      string[] args = Environment.GetCommandLineArgs();
      if (args.Length > 1)
         GetPageSizeAsync(args[1]).Wait();
      else
         Console.WriteLine("Enter at least one URL on the command line.");
   }

   private static async Task GetPageSizeAsync(string url)  
   {  
       var client = new HttpClient();  
       var uri = new Uri(Uri.EscapeUriString(url));
       byte[] urlContents = await client.GetByteArrayAsync(uri);
       Console.WriteLine($"{url}: {urlContents.Length/2:N0} characters");  
   }  
}
```

> 任何异步操作都可以使用,多用于Task<>  
> 好东西,可以用来做PRC

本质是编译成状态机一步步向下执行

## 调用方信息特性

可以通过特性获得调用自己的其他程序信息.

```CSharp
public void DoProcessing()
{
    TraceMessage("Something happened.");
}

public void TraceMessage(string message,
        [System.Runtime.CompilerServices.CallerMemberName] string memberName = "",
        [System.Runtime.CompilerServices.CallerFilePath] string sourceFilePath = "",
        [System.Runtime.CompilerServices.CallerLineNumber] int sourceLineNumber = 0)
{
    System.Diagnostics.Trace.WriteLine("message: " + message);
    System.Diagnostics.Trace.WriteLine("member name: " + memberName);
    System.Diagnostics.Trace.WriteLine("source file path: " + sourceFilePath);
    System.Diagnostics.Trace.WriteLine("source line number: " + sourceLineNumber);
}
```

```CSharp
public static void ValidateArgument(string parameterName, bool condition, [CallerArgumentExpression("condition")] string? message=null)
{
    if (!condition)
    {
        throw new ArgumentException($"Argument failed validation: <{message}>", parameterName);
    }
}
public void Operation(Action func)
{
    ValidateArgument(nameof(func), func is not null);
}
```

|特性| 	描述 |	类型|
|---|---|---|
|CallerFilePathAttribute |	包含调用方的源文件的完整路径. 这是编译时的文件路径. |String
|CallerLineNumberAttribute |	源文件中调用方法的行号. 	|Integer
|CallerMemberNameAttribute |	调用方的方法或属性名称. 请参阅本主题后面的成员名称. 	|String
|CallerArgumentExpressionAttribute|	参数表达式的字符串表示形式|	String

调用方信息特性让你可以轻松检索上下文的信息，不需要采用大量样本反射代码。 这在诊断和日志记录任务中也很有用

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
