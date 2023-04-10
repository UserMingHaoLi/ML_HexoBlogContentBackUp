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

# 快速开始

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

> `位`,`字节`,`字`是计算机数据存储的单位,`位`是*最小的存储单位*,每一个`位`存储一个*二进制*码(即`0`或`1`),一个`字节`由**8位**组成。而`字`通常为*16*,*32*,*64*个`位`组成(取决于平台)

可为空的值类型暂时没有记录在其中,它属于`System.Nullable<T>`实例  
每个值类型都继承自->`ValueType`->`Object`  

官方**推荐使用关键字**而不是类名,因为编译器做了很多优化.

> 对于`Bool`类型,CIL的认为它是1字节的,但实际实现的时候由于种种原因(内存对齐,平台,等...),可能超过这个大小.

# 简介

值类型的变量包含类型的实例  
在传递值类型的过程中,总是会复制相应的类型实例  
> 除非显示声明例外`ref`,`out`等..

> 若要使代码更不易出错、更可靠,请定义并使用不可变的值类型。

值类型可以是  
* 结构类型(`struct`)
* 枚举类型
* 值元组
> 还有一个可为Null的值类型

目前,C#中有以上的值类型.  
其中前10个是整数类型,后续是3个浮点类型,然后是bool,char,Enum,Struct,值元组.

# 特殊的`char`

`char`实际被微软认为是整数类型,但又比较特殊.
可以隐式转化为一些整数类型,最后得到Unicode编码的序号.
```CSharp
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

---

# 字面量

所有的整数类型可以通过文本来直接描述

* 二进制 使用`0b`或`0B`
* 十进制 正常表示即可
* 十六进制 使用`0X`或`0X`

> 从 C# 7.0 开始提供用`_`分割文本字面量的支持

指定无符号,使用后缀`u`或`U`  
指定长整型,使用后缀`l`或`L`  
> 使用小写`l`将会生成一个警告,因为他和`1`非常相近.

> 如果字面量所表示的值超出了 `UInt64.MaxValue`,则将出现编译器错误.  
> 如果文本的值不在目标类型的范围内,则发生编译器错误,但使用强制转换不会发生编译错误(有可能发生运行时错误,这取决于项目设置)
> 使用关键字`checked`, `unchecked`可以指定区块是否检查运算和转换的溢出, 也就是异常`System.OverflowException`

# 浮点数

**使用标准`IEEE 754`**

*此标准下,浮点数可能产生非预期的`NaN`和`Infinity`, 具体等源码再讲.*

常规的字面计算,其结果都默认为整形,若想声明字面量为浮点,则需要使用后缀`F`,`D`,`M`  
整形之间的计算结果也会得出整形,若想要得出浮点类型,则需要至少其中一方为浮点类型.此时可以使用强制转化.  
对于混合类型的预算,总是转型到式子中最高的类型,也就是有`float`就转化为`float`,之后是`double`,`dicimal`

*此标准下,浮点数总有不能精确表示的值.*

浮点数仅只能精准表示分母为2的幂的数字,如果不是,则精度必有损失.  
想要能准确表示的浮点数,建议使用`decimal`.  
精准就意味着存储量变少,`decimal`占有最大的空间,但只能描述较少的范围.

decimal 类型和 float 或 double 类型之间不存在隐式转换

**任何整型数值类型都可以隐式转换为任何浮点数值类型**

# bool类型

C#的`bool`类型是独特的, 不存在整数到bool的隐式转化.

> 注意: 对于可为null的bool类型,有更复杂的运算结果集.

# 枚举类型

默认情况下,枚举成员的关联常数值为类型 int；它们从零开始,并按定义文本顺序递增 1  
可以显式指定任何其他整数数值类型作为枚举类型的基础类型。 还可以显式指定关联的常数值  
枚举类型 E 的默认值是由表达式 (E)0 生成的值,即使零没有相应的枚举成员也是如此

> 如果没有0值代表的枚举,则(E)0的结果总是0.

如果要使用组合枚举,则需要声明`[Flags]`特性.  
如果不声明此特性也可以使用,但是需要自己定义计算方式.

```CSharp
[Flags]
public enum Days
{//定义可组合枚举时,建议使用`0B`格式,方便查看和记忆.
    None      = 0b_0000_0000,  // 0
    Monday    = 0b_0000_0001,  // 1
    Tuesday   = 0b_0000_0010,  // 2
    Wednesday = 0b_0000_0100,  // 4
    Thursday  = 0b_0000_1000,  // 8
    Friday    = 0b_0001_0000,  // 16
    Saturday  = 0b_0010_0000,  // 32
    Sunday    = 0b_0100_0000,  // 64
    Weekend   = Saturday | Sunday
}
```

如果上述`enum`没有定义`[Flags]`特性的类型,则不能使用枚举字面值表示中间数字.

例子:  

```CSharp
Console.WriteLine((Days)3);
Console.WriteLine((Days)96);
```
*有[Flags]*
```
Monday, Tuesday
Weekend
```
*无[Flags]*
```
3
Weekend
```

> 有兴趣可以扩展阅读`Enum.HasFlag`方法

> C#7.3开始可以使用`System.Enum`约束,此约束总满足`struct`约束,且是不可为`null`的值类型

> 使用枚举要注意可能存在的装箱拆箱

# 结构类型

使用`struct`来定义自己的结构类型

例子:
```CSharp
public struct Coords
{
    public Coords(double x, double y)
    {
        X = x;
        Y = y;
    }

    public double X { get; }
    public double Y { get; }

    public override string ToString() => $"({X}, {Y})";
}
```

> C#的值类型都是`struct`

# 元组类型

将多个数据组合到一起,称为元组

```
(double, int) t1 = (4.5, 3);
```

元组类型是值类型,元组元素是公共字段,这使得元组为**可变的值类型**

目前的元组都是`System.ValueTuple`类型,但是你也可以使用`System.Tupl`类型.  
* `ValueTuple` 类型是值类型。 `Tuple` 类型是引用类型。
* `ValueTuple` 类型是可变的。 `Tuple` 类型是不可变的。
* `ValueTuple` 类型的数据成员是字段。 `Tuple` 类型的数据成员是属性。

# 可为空的值类型

任何可为空的值类型都是泛型 `System.Nullable<T>` 结构的实例

定义可为空的值类型,需要在对于值类型后跟上一个`?`符号.  
如 
```CSharp
bool? isValue = null
```

故名思意,此种类型的值可以为`null`  
所以使用时经常需要` Nullable<T>.HasValue `来检查.  
或使用`is`运算符

```CSharp
int? a = 42;
if (a is int valueOfA)
{
    Console.WriteLine($"a is {valueOfA}");
}
```

可以使用`c != null`来比较,但还是推荐使用`HasValue`

也可以使用`Null合并运算`来转化可空值类型  
```CSharp
int? a = 28;
int b = a ?? -1;
```

在运行时,如果可为空的值类型的值为 null,则显式强制转换将抛出 `InvalidOperationException`

> 一般来说,只建议在与数据库交互的时候使用可为空的值类型.  
> 其他任何时候,这种类型只会成倍的增加维护成本.

## 运算符

由于多出了一个`null`值的维度,很多的运算符结果会发生意想不到的变化.

```CSharp
int? a = 10;
int? b = null;
int? c = 10;

a++;        // a is 11
a = a * c;  // a is 110
a = a + b;  // a is null
```

这表明,`null`有传播性.  
> 对于 `bool?` 类型,预定义的 `&` 和 `|` 运算符不遵循此部分中描述的规则：即使其中一个操作数为 `null`,运算符计算结果也可以不为 null, 若一定要使用,建议查表

对于比较运算符,可能得到一个不在区间内的结果.  
即  
* 既不大于等于 null,
* 也不小于 null

```CSharp
int? a = 10;
Console.WriteLine($"{a} >= null is {a >= null}");
Console.WriteLine($"{a} < null is {a < null}");
Console.WriteLine($"{a} == null is {a == null}");
// 10 >= null is False
// 10 < null is False
// 10 == null is False
```

```CSharp
int? b = null;
int? c = null;
Console.WriteLine($"null >= null is {b >= c}");
Console.WriteLine($"null == null is {b == c}");
```

对于相等运算符 `==`,如果两个操作数都为 null,则结果为 true；如果只有一个操作数为 null,则结果为 false；否则,将比较操作数的包含值。

对于不等运算符 `!=`,如果两个操作数都为 null,则结果为 false；如果只有一个操作数为 null,则结果为 true；否则,将比较操作数的包含值。

## 装箱和拆箱

如果 `HasValue` 返回 `false`,则生成空引用。
如果 `HasValue` 返回 `true`,则基础值类型 T 的对应值将装箱,而不对 Nullable<T> 的实例进行装箱.

如果要确定实例是否是可为空的值类型,请不要使用 `Object.GetType` 方法, 而是使用`typeof`关键字  
因为使用`Object`的方法,则该实例将装箱到 `Object`, 此时结果可能为`T`的基础类型`Type`

另外,请勿使用 is 运算符来确定实例是否是可为空的值类型  

```CSharp
int? a = 14;
if (a is int)
{
    Console.WriteLine("int? instance is compatible with int");
}

int b = 17;
if (b is int?)
{
    Console.WriteLine("int instance is compatible with int?");
}
// Output:
// int? instance is compatible with int
// int instance is compatible with int?**
```

可使用以下示例中提供的代码来确定实例是否是可为空的值类型

```CSharp
int? a = 14;
Console.WriteLine(IsOfNullableType(a));  // output: True

int b = 17;
Console.WriteLine(IsOfNullableType(b));  // output: False

bool IsOfNullableType<T>(T o)
{
    var type = typeof(T);
    return Nullable.GetUnderlyingType(type) != null;
}
```

> 看完这些警告和规范,相信你已经了解到了可控值类型的麻烦之处. 如非必要,切勿使用. 用了也赶快转为正常类型.

# 更多

有关更多信息,请参阅 C# 语言规范的以下部分：

* 可以为 null 的类型
* 提升的运算符
* 隐式可为空转换
* 显式可为空转换
* 提升的转换运算符


C# 参考

* "提升"的准确含义是什么？
* System.Nullable<T>
* System.Nullable
* Nullable.GetUnderlyingType
* 可为空引用类型

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->


<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
