---
title: 类解析-Object
date: 2021-05-12 21:32:00
updated: 2021-05-12 21:32:00
id: ml-20210512-213200-g139
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

语言通常不需要类来声明继承 `Object` ，因为继承是隐式的

因为 .NET 中的所有类都是从派生的 `Object` ，所以类中定义的每个方法 `Object` 都可用于系统中的所有对象

所以, 任何类都有以下方法

*  public virtual bool Equals(Object? obj);
*  public virtual int GetHashCode();
*  public Type GetType();
*  public virtual string? ToString();

> 注意到了吗,`GetType`是无法重写的,这防止类型隐瞒自身真实的`Type`

然后`Object`还保留一个  
* protected Object MemberwiseClone();

之后是两个静态方法  
* Equals(Object? objA, Object? objB);
* ReferenceEquals(Object? objA, Object? objB);

> 还有构造器`public Object();`,和构析器`~Object();`

<!--more-->

# 性能注意事项

对类型进行装箱和取消装箱的过程会产生性能开销

* 创建接受类型的常规方法 `Object` ，并创建一组特定于类型的方法重载，这些重载接受你希望类经常处理的每个值类型

* 将类型及其成员设计为使用泛型

# Equals

```C#
public virtual bool Equals(Object obj)
{
	return RuntimeHelpers.Equals(this, obj);
}
//RuntimeHelpers.Equals 如下
[System.Security.SecuritySafeCritical]  // auto-generated
[ResourceExposure(ResourceScope.None)]
[MethodImplAttribute(MethodImplOptions.InternalCall)]
public new static extern bool Equals(Object o1, Object o2);
```

使用object的比较可能产生额外性能开销,建议使用
```C#
interface IEquatable<T>
```
*不过并不能确认外部调用了那个`Equals`,所以还是要重写`Object`的`Equals`*

* 如果当前实例是引用类型，则 `Equals(Object)` 方法会测试引用相等性，并调用 `Equals(Object)` 方法等效于调用 `ReferenceEquals` 方法
  * 可以重写引用类型的的默认实现 `Equals` ，以测试值相等性而不是引用相等性，并定义值相等性的精确含义
* 如果当前实例是值类型，则方法会 `Equals(Object)` 测试值是否相等
  * 对于值类型，应始终重写 `Equals` ，因为依赖于反射的相等性测试会**降低性能**
* 枚举	`Enum.Equals`	值必须具有相同的枚举类型和基础值。
* 委托	`MulticastDelegate.Equals`	委托必须具有相同的调用列表类型。
* 接口	`Object.Equals(Object)`	引用相等性。

例如，对象的值 `String` 基于字符串的字符; `String.Equals(Object)` 方法会重写 `Object.Equals(Object)` 方法，以便 `true` 为任意两个包含相同字符的字符串实例返回相同的顺序

重写示例
```C#
public override bool Equals(Object obj)
{
	Person personObj = obj as Person;
	if (personObj == null)
		return false;
	else
		return idNumber.Equals(personObj.idNumber);
}
```

**对于相等性比较,要注意一致,传递,交换,Null**  
* `Equals` 不得引发异常*

重写时，请遵循以下准则 `Equals(Object)` ：

* 实现的类型 `IComparable` 必须重写 `Equals(Object)` 
* 重写的类型 `Equals(Object)` 还必须重写 `GetHashCode` ; 否则，哈希表可能无法正常工作
* 应考虑实现 `IEquatable<T>` 接口，以支持强类型化测试是否相等。 你的 `IEquatable<T>.Equals` 实现应返回与`Equals(Object)`一致的结果 
* 对于`==`和`Equals(Object)`,应该一并重载,并得出一致的结果
  * 大多数引用类型不得重载相等运算符（即使它们重写`Equals`） 。 但是，如果要实现的引用类型应具有值语义，则必须重写相等运算符
* 不应覆盖 `Equals` 可变引用类型,这意味着可变引用类型的实例的哈希代码在其生存期内可能会更改,导致对象在哈希表中丢失




# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
