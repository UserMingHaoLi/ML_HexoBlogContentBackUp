---
title: CSharp编程指南_泛型类型约束关键字
date: 2021-04-24 19:43:00
updated: 2021-04-24 19:43:00
id: ml-20210424-194300-g129
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

就是常用的`where`约束,以及其后跟的关键字.

泛型定义中的 `where` 子句指定对用作泛型类型、方法、委托或本地函数中类型参数的参数类型的约束

例1
```CSharp
class ItemFactory<T> where T : new()
{
    public T GetNewItem()
    {
        return new T();
    }
}
```
例2
```CSharp
public class ItemFactory2<T>
    where T : IComparable, new()
{  }
```

可以声明一个泛型类 `AGenericClass`，以使类型参数 `T` 实现 `IComparable<T>` 接口

```CSharp
public class AGenericClass<T> where T : IComparable<T> { }
```

<!--more-->

`where` 子句还可包括基类约束。 基类约束表明用作该泛型类型的类型参数的类型具有指定的类作为基类（或者是该基类）。 该基类约束一经使用，就必须出现在该类型参数的所有其他约束之前

某些类型不允许作为基类约束：`Object`、`Array` 和 `ValueType`。   
在 `C# 7.3` 之前，`Enum`、`Delegate` 和 `MulticastDelegate` 也不允许作为基类约束

# 类型参数的约束

* `where T : struct`
  * 不可为 null 的值类型
* `where T : class`
  * 引用类型
  * 在 `C#8.0` 或更高版本中的可为 `null` 上下文中，`T` 必须是不可为 `null` 的引用类型
* `where T : class?`
  * 可为 null 或不可为 null 的引用类型
* `where T : notnull`
  * 不可为 null 的类型
* `where T : unmanaged`
  * 不可为 null 的非托管类型
* `where T : new()`
  * 具有公共无参数构造函数
* `where T : <base class name>`
  * 指定的基类或派生自指定的基类
  * 在 `C#8.0` 或更高版本中的可为 `null` 上下文中，`T` 必须是不可为 `null` 的引用类型
* `where T : <base class name>?`
  * 指定的基类或派生自指定的基类
* `where T : <interface name>`
  * 指定的接口或实现指定的接口
  * 在 `C#8.0` 或更高版本中的可为 `null` 上下文中，`T` 必须是不可为 `null` 的引用类型
* `where T : <interface name>?`
  * 指定的接口或实现指定的接口
* `where T : U`
  * 为 T 提供的类型参数必须是为 U 提供的参数或派生自为 U 提供的参数

请避免对类型参数使用 == 和 != 运算符  
如有需要,请使用`where T : IEquatable<T>` 或 `where T : IComparable<T>` 约束

> 从 `C# 7.3` 开始，可将 `System.Delegate` 或 `System.MulticastDelegate` 用作基类约束

```CSharp
public static TDelegate TypeSafeCombine<TDelegate>(this TDelegate source, TDelegate target)
    where TDelegate : System.Delegate
    => Delegate.Combine(source, target) as TDelegate;
```

> 从 `C# 7.3` 开始，还可指定 `System.Enum` 类型作为基类约束

```CSharp
public static Dictionary<int, string> EnumNamedValues<T>() where T : System.Enum
{
    var result = new Dictionary<int, string>();
    var values = Enum.GetValues(typeof(T));

    foreach (int item in values)
        result.Add(item, Enum.GetName(typeof(T), item));
    return result;
}
enum Rainbow
{
    Red,
    Orange,
    Yellow,
    Green,
    Blue,
    Indigo,
    Violet
}
//---
var map = EnumNamedValues<Rainbow>();
foreach (var pair in map)
{
    Console.WriteLine($"{pair.Key}:\t{pair.Value}");
}
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
