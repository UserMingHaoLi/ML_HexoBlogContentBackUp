---
title: CSharp编程指南_方法参数关键字
date: 2021-04-23 20:39:00
updated: 2021-04-23 20:39:00
id: ml-20210423-203900-g126
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

# 快速参考

为不具有 `in`、`ref` 或 `out` 的方法声明的参数会按值传递给调用的方法。 可以在方法中更改该值,但当控制传递回调用过程时,不会保留更改后的值。 可以通过使用方法参数关键字更改此行为.

* `params` 指定此参数采用可变数量的参数。
* `in` 指定此参数由引用传递,但只由调用方法读取。
* `ref` 指定此参数由引用传递,可能由调用方法读取或写入。
* `out` 指定此参数由引用传递,由调用方法写入。

<!--more-->

# params

使用 `params` 关键字可以指定采用数目可变的参数的方法参数。 参数类型必须是一维数组

在方法声明中的 `params` 关键字之后不允许有任何其他参数,并且在方法声明中只允许有一个 `params` 关键字

使用 params 参数调用方法时,可以传入：
* 数组元素类型的参数的逗号分隔列表。
* 指定类型的参数的数组。
* 无参数。 如果未发送任何参数,则 `params` 列表的长度为`零`

```CSharp
public static void UseParams(params int[] list)
{
	for (int i = 0; i < list.Length; i++)
	{
		Console.Write(list[i] + " ");
	}
	Console.WriteLine();
}
```

# in

`in` 关键字会导致按引用传递参数,但确保未修改参数
```CSharp
int readonlyArgument = 44;
InArgExample(readonlyArgument);
Console.WriteLine(readonlyArgument);     // value is still 44

void InArgExample(in int number)
{
    // Uncomment the following line to see error CS8331
    //number = 19;
}
```
*调用处通常不需要 `in` 修饰符。 仅在方法声明中需要它*

> `in` 关键字还作为 `foreach` 语句的一部分,或作为 `LINQ` 查询中 `join` 子句的一部分,与泛型类型参数一起使用来指定该类型参数为逆变

*`in` 参数修饰符可在 `C# 7.2` 及更高版本中使用*

**`in`、`ref` 和 `out` 关键字不被视为用于重载决议的方法签名的一部分**

*定义使用 `in` 参数的方法是一项潜在的性能优化*  
 某些 `struct` 类型参数可能很大,在紧凑的循环或关键代码路径中调用方法时,复制这些结构的成本就很高  
 这能节省一些性能,又不会造成更多安全隐患

> 为了简化操作,前面的代码将 `int` 用作参数类型。 因为大多数新式计算机中的引用都比 `int` 大,所以将单个 `int` 作为只读引用传递没有任何好处

不能将 `in`,`ref` 和 `out` 关键字用于以下几种方法：
异步方法,通过使用 `async` 修饰符定义。
迭代器方法,包括 `yield return` 或 `yield break` 语句。
扩展方法的第一个参数不能有 `in` 修饰符,除非该参数是结构。
扩展方法的第一个参数,其中该参数是泛型类型（即使该类型被约束为结构。）

# ref

`ref` 关键字指示按引用传递值

* 在方法签名和方法调用中,按引用将参数传递给方法
* 在方法签名中,按引用将值返回给调用方, 指`return ref`
* 在成员正文中,指示引用返回值是否作为调用方欲修改的引用被存储在本地。 或指示局部变量按引用访问另一个值。
* 在 `struct` 声明中,声明 `ref struct` 或 `readonly ref struct`

> 当通过引用传递时,不会对值类型装箱

```CSharp
void Method(ref int refArgument)
{
    refArgument = refArgument + 44;
}
int number = 1;
Method(ref number);
Console.WriteLine(number);
// Output: 45
```

## 引用返回值

```CSharp
public static ref int Find(int[,] matrix, Func<int, bool> predicate)
{
    for (int i = 0; i < matrix.GetLength(0); i++)
        for (int j = 0; j < matrix.GetLength(1); j++)
            if (predicate(matrix[i, j]))
                return ref matrix[i, j];
    throw new InvalidOperationException("Not found");
}
```
*调用此方法,并修改其返回值,实际结果可以反应到数组中.*

> 调用方法还可能会将返回值声明为 `ref readonly` 以按引用返回值,并坚持调用代码无法修改返回的值  
> 这和上面的`in`是差不多的理念

## ref 局部变量

ref 局部变量用于指代使用 return ref 返回的值
```CSharp
ref decimal estValue = ref Find(matrix, predicate);
```
所以调用上面返回引用的方法就需要这个.

也可以直接转化为临时引用对象
```CSharp
ref VeryLargeStruct reflocal = ref veryLargeStruct;
```

> 从 `C# 7.3` 开始,`foreach` 语句的迭代变量可以是 `ref` 局部变量,也可以是 `ref readonly` 局部变量  
> 从 `C# 7.3` 开始,可以使用 `ref` 赋值运算符重新分配 `ref local` 或 `ref readonly local` 变量

## Ref readonly 局部变量

`Ref readonly` 局部变量用于指代在其签名中具有 `ref readonly` 并使用 `return ref` 的方法或属性返回的值
所以说用法同上,只不过是拿`return ref readonly`的返回值

# out 关键字通过引用传递参数

想用`out` 也可以参阅下`元组`

`out`与 `ref` 关键字相似,只不过 `ref` 要求在传递之前初始化变量  
作为 `out` 参数传递的变量在方法调用中传递之前不必进行初始化。 但是被调用的方法需要在返回之前赋一个值

```CSharp
int initializeInMethod;
OutArgExample(out initializeInMethod);
Console.WriteLine(initializeInMethod);     // value is now 44

void OutArgExample(out int number)
{
    number = 44;
}
```

> `out` 关键字也可与泛型类型参数结合使用,以指定该类型参数是协变参数

使用 `out` 参数声明方法是返回多个值的经典解决方法。 自 `C# 7.0` 起,可以考虑针对类似方案使用`值元组`

从 `C# 7.0` 开始,可以在方法调用的参数列表而不是单独的变量声明中声明 `out` 变量

```CSharp
Int32.TryParse(numberAsString, out int number)
Int32.TryParse(numberAsString, out var number) //var也可以
```

## 小结

所以大部分用途其实是性能优化,或者修改目标本体的值.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
