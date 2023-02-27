---
title: CSharp编程指南_引用类型
date: 2021-04-12 20:30:00
updated: 2021-04-12 20:30:00
id: ml-20210412-203000-g118
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

# 引用类型

根据C#定义,包含以下类型.

* class]
  * object
  * dynamic
  * string
* interface
* array
* delegate

> 特别注意,`null`不是引用类型,但与所有引用类型兼容,代表此引用不包含实例.


<!--more-->

# 预定义的特殊引用类型

| 类型             | 说明                   |
| ---------------- | ---------------------- |
| System.Object    | 所有其他类型的最终基类 |
| System.String    | C # 语言的字符串类型   |
| System.ValueType | 所有值类型的基类       |
| System.Enum      | 所有枚举类型的基类     |
| System.Array     | 所有数组类型的基类     |
| System.Delegate  | 所有委托类型的基类     |
| System.Exception | 所有异常类型的基类     |

> 还有我们值类型说过的,几个特殊值类型`System.Nullable<T>`和`System.ValueTuple`

# System.Object

`object`类型是所有其他类型的最终基类.  
C # 中的每个类型都直接或间接从 `object` 类型派生.

> 后续有机会讲一下`object`源码

# 动态类型

`dynamic`类型可以引用任何对象,编辑器不会检查任何和`dynamic`相关的语句,但运行时会检查.  
所以错误可能推迟到运行时,将引发异常.

*`dynamic`和`object`视为同样的签名*

# 字符串类型

`string`类型是直接从`object`继承的密封类, `string` 表示 `Unicode` 字符串, 也就是`char`的合集

`string`支持直接使用字符串字面量赋值.

尽管`string`是引用类型,但是仍然可以使用`==`,`!=`来对其进行逐字的比较,这是C#做的优化.  
这使得对字符串相等性的测试更为直观

```CSharp
string a = "hello";
string b = "h";
b += "ello";//此时生成新的引用字符串
Console.WriteLine(a == b);//true 逐字比较相等
Console.WriteLine(object.ReferenceEquals(a, b));//false 引用不相等
```

上述使用的`+`运算符并非真的进行了链接操作,而是使用两边的字符串生成了一个新的字符串, 所以内存中拥有三个字符串实例.

**字符串是不可变的**,所有看起来改变了值的字符串,都是新的字符串实例.  
若要使用可变字符串来节省内存,请参考`StringBuilder`类.

由于字符串实际是`char`的合集,所以也可以使用`\u0066`这种写法,但是要注意对`\`的转义

# 数组类型

数组内的元素可以是引用类型或者值类型,但数组本体是引用类型的.  
所以通过数组本体赋值操作,可以从多个地方访问同一个数组.

# 委托类型

类似于其他语言中的方法指针,此引用类型引用了一个方法的地址.

# 装箱和拆箱

由于值类型`ValueType`也继承至`object`  
所以任何值类型可以向`object`进行强制转换而不损失任何数据.  

此时将会吧值类型的值放置于堆中, 之后通过`object`引用访问就是引用类型.  
下次强制转化会值类型, 也只是从堆中拷贝一份. 这样保持值类型的特性.

概念上大概如下所示,但实际实现有差别.

```CSharp
int i = 123;
object box = new Box<int>(i);
```
继续看如下代码
```CSharp
Point p = new Point(10, 10);
object box = p;
p.x = 20;
Console.Write(((Point)box).x); //输出10
```
这时我们知道了,box在堆中创建的内容,实际上只是拷贝.  

如果我们修改一下
```CSharp
Point p = new Point(10, 10);
object box = p;
((Point)box).x = 20;//Error: 无法修改取消装箱转化的结果
Console.Write(((Point)box).x);
```
想向box内的x赋值是不行的,因为box是`object`类型,没有`x`字段.  
所以需要强制转化回`Point`类型,但是编辑器提示无法修改,这是因为一定进行转化,就会赋值一份堆上的数据到栈上,但是此时并没有任何临时变量接住这个栈上的值, 所以最后此次修改必定被丢弃,于是编译器阻止了此操作.

*拆箱的概念大概如下,实际实现有差异*
```CSharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

所以如果没有`i`,那么对于拆出来值的修改,都无法在正文中的到.

# class

即用户自定义类型

如下
```CSharp
class Queue {...}
class Queue<TElement> {...}
```

# record

C#9.0提供,暂时不讲.  
主要用于提供类似于`string`的**值相等性**

# 表达式树

可以允许你使用代码生成表达式, 很强大,建议专门学习.


# 可为null的引用类型

目前,所有引用类型都是可为`null`的.但是再`C#8.0`之后,你可以通过编译器设置来指定默认所有引用类型不可为`null`  
此时,于可为`null`的值类型相同,可通过`T?`来声明可为`null`的引用类型.  

这主要是为了避免永无止境的`t == null`的判断,之前也出过`null传播符`和`null合并运算`. 现在总算是除了强制不可为`null`

**编译器不会为不可为 null 的引用类型添加任何运行时检查**  
只在编译器层面报出警告或者错误,这取决于你的项目设置.

# void

`void`不属于任何类型,通常用于返回值,表示不返回任何值.  
有些时候还用作表示指向未知类型的指针, 所以归类为引用类型吧.

# var

由编译器自动推断,运行时不存在var这种类型.

# 默认值

对于任何引用类型,如果只定义,不赋值.  
那么默认值都是`null`  
如果开启了不可为`null`模式,则编译器提示你一个警告或者错误.

> 在运行时,如果 `System.Type` 实例表示一个值类型,则可以使用 `Activator.CreateInstance(Type)` 方法来调用无参数构造函数,以获取该类型的默认值.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
