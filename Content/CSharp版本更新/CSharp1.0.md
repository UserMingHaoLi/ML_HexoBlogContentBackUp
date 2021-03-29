---
title: hexo模板
date: 2021-03-29 22:11:00
updated: 2021-03-29 22:11:00
id: ml-20210329-221100-g106
categories:
	- Hexo
tags: 
	- Hexo
---

# C# 1.0

>本系列均参考自
>>[微软官方文档][MicrosoftDoc]
>>[博客园][BoKeYuanCSharp]

根据微软官方[C#发展历史][AddLink2], 此时的c#主要功能包括

* 类
* 结构
* 接口
* 事件
* 属性
* 委托
* 表达式
* 语句
* 特性
* 文本

<!--more-->

## C#1.0是C#最初始的版本

由于创建时间较晚, 良好的吸取了前人经验, 拥有面向对象架构的同时拥有一些自己独特的东西. 
如果只是想入门C# 可以参阅 [微软官方C#指南][AddLink1]  
本文提供的是C#历史变更记录, 有时候会涉及到一些略微底层的东西, 有利于程序员的灵魂.

## 面向对象基本特性 -**封装**, **继承**, **多态**

任何一门现代面向对象语言都会有的基本能力

### **封装**

提供对实现细节的隐藏

具体实现可以参考[MSDN访问修饰符(C#参考)][link1]

简单讲一下大概就是以下四个关键字

```C#
public //公开的
protected //仅继承的
internal //仅当前程序集的
private //私有的
```

但又有两组关键字组合的访问修饰符

```C#
protected internal  //在程序集中和继承中都可访问
private protected   //C#7.2新增 在继承类中访问私有成员
```

### **继承**

在类的声明是使用`:`来进行继承类或者实现接口, 也可以继续跟`,`来继续实现接口  
如下

```C#
interface ISon
{
    //接口
}
public class Base
{
    //父类
}
public class Son:Base, ISon
{
    //子类, 继承, 实现接口
}
public class IInterfaceSon:Ison
{
    //仅实现接口
}
```
### **多态**

多态可以说是面向对象编程的关键, 非一日之功, 不是一时半会可以讲清楚的.  
我只说一个大概吧  
继续刚刚的类, 我们写一个Main
```C#
public void Main()
{
    Base m_base = new Son();    //声明一个父类, 实际里面装的子类
    ISon m_Son = new Son();     //声明一个接口, 里面装的一个实现接口的类
    //调用的时候内容以声明的为准.
}
```

以上就是一个最简单的多态.  
更细的资料可以 [百度][link2] OR [Google][link3]

## 数据类型 -**值类型**, **引用类型**

数据就是程序的根本, 任何语言都应该有数据类型 哪怕是`var`  
如果有没有数据类型的语言, 请联系我.

C# 充分吸取教训, 既有常用的值类型, 也有更灵活的引用类型, 还支持自定义值类型`struct`

### **值类型**

可以参阅[MSDN 值类型][link4] 内有值类型相关详细定义和关键字说明

我们这里只讲值类型的根本和实际操作中的易忽略项.

例如: 值类型的深拷贝

>所有值类型的赋值, 实际上都是将自己完全赋值一份, 也就是说 从一份变成了两份

```C#
int a = 1;
int b = a;
b = 3;
print(a)
print(b)
```

输出

```C#
1
3
```

你可能觉得没什么了不起的, 你早就知道了, 那么再看看下面这个

```C#
public int Selfadd(int self)
{
    self = self + 1
    return self;
}
public void Main()
{
    int a = 1;
    int b = a;
    int c = Selfadd(a)
    print(a,b,c)
}
```

输出`1,1,2`

当你传递一个值类型的参数到其他方法内, 实际上是声明了一个新的变量  
类似于

```C#
int self = a
```

这里还牵扯到堆栈问题, 感兴趣的同学可以搜索一下堆栈相关, 这个是程序运行的基本.

对了 所有值类型都派生自`Object.System.ValueType`  
也就是说 值类型其实也是引用类型, 这就涉及到了 **装箱和拆箱** 等下讲.

再说一下 [`struct`][link5]

你可以像定义引用类型一样定义一个值类型
```C#
public struct Book
{
    public decimal price;
    public string title;
    public string author;
}
```

### **引用类型**

引用类型与值类型最大的不同就是浅拷贝  下面我们细讲

当你声明一个引用类型的时候

```C#
M_Class A = new M_Class();
```

此时 A并没有装下一个M_Class, 而是装下了一个M_Class的地址  
有些类似于快递, 快递单上只是标有你家的地址, 但是快递会准确的送到你家.

此时我们重复之前值类型的操作试试.

```C#
M_Class A = new M_Class();
M_Class B = A;
B.num = 3;
print(B.num)
print(B.num)
```

你可能猜到了 没错! 都是`3`

这是因为`A` 和 `B` 都拥有同一个地址, 最后快递当然会到达同一个地方.

最后还是不得不讲一下堆栈.  
如果你学过一点点C或者C++ 这应该不难.

当你声明一个 `A` 的时候, 实际上是在 栈上分配了一个空间(大小根据操作系统而定, 但都很小)

里面实际上只存储了一小串堆的地址  
大概类似于 `FABC11` 这种  
这串地址在计算机中意味着一段新内存, 计算机通过解析这段地址到达一个新的地方, 也就是

`new M_Class()` 做的事情, 他在堆上分配了一个大空间, 大到足以容纳整个`M_Class`

我们只是通过A来查找之前 `new` 出来的M_Class而已.

所以, 当`B=A` 实际B的内容和A一样 都是 `FABC11` 所以他们都指向同一个 `M_Class` .  
当B修改, 最后A访问的时候, 得到的是修改后的值.

不必执行值类型里面的第二个例子了, 大家都知道, 即使传递引用类型到函数内去, 函数一样可以根据引用查到堆里面, 改写内存, 于是内容就发生了改变.


### **装箱和拆箱**

之前提到了 
>所有值类型都派生自`Object.System.ValueType` 

但是我们知道, `Object` 是一个引用类型, 其子类也是引用类型, 那为什么会有值类型呢.

这就是微软变得一个戏法了.

实际上我们可以通过向父类转型来将一个值类型变成一个引用类型

```C#
int a = 1
Object obj = (Object)a
```

这样 就将原本的值类型包装成了一个引用类型, 这就叫装箱.

相反的

```C#
a = (int)obj
```
这是拆箱

其中 在一个变量前使用(int)或者是(Object)或者是其他任何类型(?), 这是一种C#语法, 叫做强制转换. 可以将一个类型强制转换为另一个类型, 但请在你有把握的时候使用,因为编译器不会帮你做检查. 可能导致严重错误.

另外 在以前 装箱和拆箱都是严重的性能消耗, 不过现在你**不需要在意**太多.

你还可以通过`ICloneable`接口来定义你自己的拷贝. 具体自己查询吧.

## 集合

大概就是 List Array Queue Stack Dictionary 这种吧.

是大量同类型数据的集, 并提供了一些读取,写入, 群体操作的基本方法. 配合`Linq`还是很好玩的.

参阅[微软 C#集合][link6]


## 迭代器

迭代器其实算是集合的一部分, 不过它太关键了, 还是拉出来讲.
所谓迭代器, 其实是两个接口 `IEnumerable` 和 `IEnumerator`

实际 `IEnumerable` 要求返回一个 `IEnumerator` 对象, 也就是说, 底层还是`IEnumerator`

来看看他们的源码

```C#
public interface IEnumerable
{
    IEnumerator GetEnumerator();
}

public interface IEnumerator
{
    object Current{get;} //当前项
    bool MoveNext();     //前进
    void Reset();        //重置
}
```

实际的所谓迭代器, 就是通过`MoveNext()`之后使用`Current`遍历全部元素而已, 你自己也可以继承这些接口, 实现自己的迭代器. `foreach` 关键字正是使用了迭代器进行遍历.

还有一个关键因素, Current只有Get属性, 也就是说, 不可能在迭代器内修改值类型.  
实际上, 不推荐在迭代器内修改任何数据, 你可以使用标志法, 即先给物体打上一个标记, 可以使用另一个集合保存被标记数据的位置, 等遍历结束后, 再进行操作.这样是比较正规的做法.


## 消息机制, 事件与代理

实际上就是 `delegate` 关键字和 `event` 关键字

windows的基调就是回调, 消息, 通知, 这些全都由以上两个关键字全权负责了.

`delegate` 实际上是一个函数的指针, 不理解指针也没关系, 你可以理解成存储了一个函数的地址, 上面讲堆栈的时候讲过了.

当你持有一个函数的地址可以干什么呢? 这就是回调函数, 具体可以[百度][link2], 持有一个函数, 你就可以在任何时间点调用它, 使你的程序具有传递性和延时性.

还有消息和通知, 其实就是讲你的函数注册到其他的地方, 由其他方法回调, 这样就打成了订阅, 通知模式, 这涉及到一种设计模式 `观察者模式`


## 多线程

太多, 太难, 不讲.

## Attribute 特性

实际上就是在类或者方法前面加上一个标签, 这样他们就有额外的元数据, 在编译时候可以进行一些基于这些额外元数据的操作.

```C#
[TestAttribute]
public struct TestStruct { }
```

这个 `TestAttribute` 就是自定义的Attribute, 你也可以使用C#自带的.

[`Obsolete`] 　
它声明一个函数被废弃, 之后会在控制台输出错误信息.

[`Conditional(DEBUG)`]
它声明一个函数只能运行在`DEBUG`宏模式下. 你可以更改()内的内容兼容其他宏

## 自动垃圾回收

其实也就是大名鼎鼎的 `GC` 关于GC你可以知道它是全自动的, 你也可以手动调用 `GC.Collect();` 强制进行, 一般我们并不会手动调用.

GC相关还有一个接口 `IDisposible` 他的同胞兄弟是 关键字 `using` 区块

```C#
using(File.Opne("XXX"))
{
    //使用完毕之后自动调用 IDisposible 接口
}
```

对了 还有一个 `~` 构析函数, 与构造函数对应, 垃圾回收会调用这些函数

## 反射和动态创建

这一块我了解不深，　只知道是一个运行时动态创建类或者修改数据的东西  
一般可以用于解析配置文件来改变程序状态．
我一般用于处理棘手的数据处理问题, 之后会重构.

## 类库

不必多言, 就是库.

> 自此 C# 1.0 就此完结  
> 最后,所有C#程序员都应该看下CIL  
> C#源码地址 https://referencesource.microsoft.com/

## C#1.2增补

随 Visual Studio 2003 一起提供的 C# 版本 1.2。 它对语言做了一些小改进。 最值得注意的是，从此版本开始，当 `IEnumerator` 实现 `IDisposable` 时，`foreach` 循环中生成的代码会在 `IEnumerator` 上调用 `Dispose`。

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[MicrosoftDoc]:https://docs.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-version-history "微软官方文档"
[BoKeYuanCSharp]:https://www.cnblogs.com/dxy1982/tag/C%23/ "博客园C#版本更细"


[link1]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/access-modifiers "C#访问修饰符 -封装"

[link2]: https://www.baidu.com "百度一下"

[link3]: https://www.google.com "谷歌官网 需要FQ"

[link4]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/value-types "C# 值类型"

[link5]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/struct "用户自定义值类型 struct"

[link6]: https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/collections "集合"

[AddLink1]: https://docs.microsoft.com/zh-cn/dotnet/csharp/index "C#指南"

[AddLink2]: https://docs.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-version-history "C#历史"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
