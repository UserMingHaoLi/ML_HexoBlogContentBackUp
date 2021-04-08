---
title: CSharp编程指南_值类型
date: 2021-04-07 23:40:00
updated: 2021-04-07 23:40:00
id: ml-20210407-234000-g115
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

# 值类型

C#共有以下内置类型,背后的class(*.NET 类型*)先不讲,以后有机会出源码讲解再说.

|     | 关键字    | .NET 类型         | 范围                                                    | 默认值  | 大小                               |
| --- | --------- | ----------------- | ------------------------------------------------------- | ------- | ---------------------------------- |
| 1   | `sbyte`   | System.SByte      | -128 到 127                                             | 0       | 有符号 8 位整                      |
| 2   | `byte`    | System.Byte       | 0 到 255                                                | 0       | 无符号的 8 位整数                  |
| 3   | `short`   | System.Int16      | -32,768 到 32,767                                       | 0       | 有符号 16 位整数                   |
| 4   | `ushort`  | System.UInt16     | 0 到 65,535                                             | 0       | 无符号 16 位整数                   |
| 5   | `int`     | System.Int32      | -2,147,483,648 到 2,147,483,647                         | 0       | 有符号 32 位整数                   |
| 6   | `uint`    | System.UInt32     | 0 到 4,294,967,295                                      | 0       | 无符号 32 位整                     |
| 7   | `long`    | System.Int64      | -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 | 0       | 有符号 64 位整                     |
| 8   | `ulong`   | System.UInt64     | 0 到 18,446,744,073,709,551,615                         | 0       | 无符号 64 位                       |
| 9   | `nint`    | System.IntPtr     | 取决于平台                                              | 0       | 有符号 32 位或64位整数(取决于平台) |
| 10  | `nuint`   | System.UIntPtr    | 取决于平台                                              | 0       | 无符号 32 位或64位整数(取决于平台) |
| 11  | `float`   | System.Single     | 1.5 × 10 ^ −45到3.4 × 10 ^ 38	 7位精度                  | 0.0f    | 4 个字节                           |
| 12  | `double`  | System.Double     | 5.0 × 10 ^ −324到1.7 × 10 ^ 308	15位精度                | 0.0d    | 8 个字节                           |
| 13  | `decimal` | System.Decimal    | 1.0 × 10 ^ −28到7.9 × 10 ^ 28	28位精度                  | 0.0m    | 16 个字节                          |
| 14  | `bool`    | System.Boolean    | `true`或`false`                                         | `false` | 1个字节(也有可能是4个或者更多)     |
| 15  | `char`    | System.Char       | U+0000 到 U+FFFF                                        | U+0000  | 16位                               |
| 16  | `Enum`    | System.Enum       | 略                                                      | 略      | (em)0                              |
| 17  | `struct ` | System.ValueType  | 略                                                      | 略      | 略                                 |
| 18  | `值元组 ` | System.ValueTuple | 略                                                      | 略      | 略                                 |

<!--more-->

> `位`,`字节`,`字`是计算机数据存储的单位,`位`是*最小的存储单位*，每一个`位`存储一个*二进制*码(即`0`或`1`)，一个`字节`由**8位**组成。而`字`通常为*16*,*32*,*64*个`位`组成(取决于平台)

可为空的值类型暂时没有记录在其中,它属于`System.Nullable<T>`实例  
每个值类型都继承自->`ValueType`->`Object`  

官方**推荐使用关键字**而不是类名,因为编译器做了很多优化.

> 对于`Bool`类型,CIL的认为它是1字节的,但实际实现的时候由于种种原因(内存对齐,平台,等...),可能超过这个大小.


目前,C#中有以上的值类型.  
其中前10个是整数类型,后续是3个浮点类型,bool,char,Enum,Struct,值元组.

`char`实际被微软认为是整数类型,但又比较特殊.
可以隐式转化为一些整数类型,最后得到Unicode编码的序号.
```C#
char @char = 'A';
//sbyte @sbyte = @char;
//byte @byte = @char;
//short @short = @char;
ushort @ushort = @char;
int @int = @char;
uint @uint = @char;
long @long = @char;
//nint @nint = @char;
//nuint @nint = @char;
Console.WriteLine(@int);//输出: 65
```
所以可以看到`A`对应的Unicode编码为65.  
*被注释的代码是无法发生隐式转化的*  
`nint`和`uint`需要9.0支持,实际上就是`IntPtr`和`UIntPtr`. 但也无法隐式转化.


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->


<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
