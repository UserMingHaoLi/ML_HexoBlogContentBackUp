---
title: CSharp编程指南_编译器读取的属性
date: 2021-05-10 23:10:00
updated: 2021-05-10 23:10:00
id: ml-20210510-231000-g138
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

"只读引用" 功能实际上是一组功能，这些功能利用通过引用传递变量的效率，但不会将数据公开给修改

* in parameters
* ref readonly 返回
* readonly 结构
* ref/in 扩展方法
* ref readonly &
* ref 条件表达式

<!--more-->

# 动机

在此功能之前，c # 没有一种有效的方法来表示将结构变量传递到方法调用以实现 readonly，而不需要进行修改

标准的按值参数传递意味着复制，这会增加不必要的成本

# in 参数

与参数类似 out  
in 参数作为托管引用传递，并具有来自被调用方的其他保证

> in 表示此参数传递引用,但目标不更改其内容.  
> 就是用来做性能优化的

对于所有用途，该 in 参数将被视为 readonly 变量。 在方法内部使用参数的大部分限制 in 都与 readonly 字段相同

# 由 readonly 引用返回

声明 ref readonly 返回成员

```CSharp
public ref readonly T ItemRef(int i)
{
	return ref this.array[i];
}
```
*这些参数也都是不可写的引用*

# 只读结构

```CSharp
readonly struct Vector3
{
}
```

# ref/in 扩展方法

```CSharp
// this can be called on either RValue or an LValue
public static void Reader(in this Guid self)
{
    // do something nonmutating.
    WriteLine(self == default(Guid));
}

// this can be called only on an LValue
public static void Mutator(ref this Guid self)
{
    // can mutate self
    self = new Guid();
}
```

# Readonly 引用局部变量

```CSharp
static readonly ref Vector3 M1() => . . .

static readonly ref Vector3 M1_Trace()
{
    // OK
    ref readonly var r1 = ref M1();

    // Not valid. Need an LValue
    ref readonly Vector3 r2 = ref default(Vector3);

    // Not valid. r1 is readonly.
    Mutate(ref r1);

    // OK.
    Print(in r1);

    // OK.
    return ref r1;
}
```

# 三元 ref 表达式

```CSharp
ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

```CSharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

文档中有一句非常经典

> 备选方法 : 主要的竞争设计实际上是 "无任何操作"。

至少他们做了.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
