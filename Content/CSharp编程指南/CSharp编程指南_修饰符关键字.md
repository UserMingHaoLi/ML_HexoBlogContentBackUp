---
title: CSharp编程指南_修饰符关键字
date: 2021-04-17 22:07:00
updated: 2021-04-17 22:07:00
id: ml-20210417-220700-g123
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

修饰符都属于关键字一类.  
关键字是预定义的保留标识符,对编译器有特殊意义.  
除非前面有 `@` 前缀,否则不能在程序中用作标识符  

# 关键字一览

任意部分关键字

|           |            |           |           |
| --------- | ---------- | --------- | --------- |
| abstract  | as         | base      | bool      |
| break     | byte       | case      | catch     |
| char      | checked    | class     | const     |
| continue  | decimal    | default   | delegate  |
| do        | double     | else      | enum      |
| event     | explicit   | extern    | false     |
| finally   | fixed      | float     | for       |
| foreach   | goto       | if        | implicit  |
| in        | int        | interface | internal  |
| is        | lock       | long      | namespace |
| new       | null       | object    | operator  |
| out       | override   | params    | private   |
| protected | public     | readonly  | ref       |
| return    | sbyte      | sealed    | short     |
| sizeof    | stackalloc | static    | string    |
| struct    | switch     | this      | throw     |
| true      | try        | typeof    | uint      |
| ulong     | unchecked  | unsafe    | ushort    |
| using     | virtual    | void      | volatile  |
| while     |

----

上下文关键字

|                       |                   |                           | 
| --------------------- | ----------------- | ------------------------- | 
| add                   | and               | alias                     |
| ascending             | async             | await                     |
| by                    | descending        | dynamic                   |
| equals                | from              | get                       |
| global                | group             | init                      |
| into                  | join              | let                       |
| nameof                | nint              | not                       |
| notnull               | nuint             | on                        |
| or                    | orderby           | partial（类型）           |
| partial（方法）       | record            | remove                    |
| select                | set               | unmanaged（泛型类型约束） |
| value                 | var               | when（筛选条件）          |
| where（泛型类型约束） | where（查询子句） | with                      |
| yield                 |

<!--more-->


# 修饰符

`访问修饰符`请参阅另一篇单独的[CSharp编程指南_访问修饰符](https://github.com/UserMingHaoLi/ML_HexoBlogContentBackUp/blob/master/Content/CSharp%E7%BC%96%E7%A8%8B%E6%8C%87%E5%8D%97/CSharp%E7%BC%96%E7%A8%8B%E6%8C%87%E5%8D%97_%E8%AE%BF%E9%97%AE%E4%BF%AE%E9%A5%B0%E7%AC%A6.md)

## abstract

用于指定抽象类和抽象方法.

```CSharp
abstract class Shape
{
    public abstract int GetArea();
}
```

抽象方法是隐式的虚拟方法(`virtual`)

## async

指定方法为异步.

说明此方法包含 `await` 语句；也包含异步操作

```CSharp
public async Task<int> ExampleMethodAsync()
{
    //...
}
```
*常用于被`await`关键字修饰调用.*  
*若如此做,此方法将会挂起并等待完成,之后再继续*  

**这是上下文关键字,只有使用在方法定义时,才作为关键字**

lambda和匿名方法都属于方法.

异步方法可以有以下返回类型
* Task
* Task<TResult>
* void
* 从 C# 7.0 开始,任何具有可访问的 `GetAwaiter` 方法的类型都可以作为异步访问
  * 参阅 `System.Threading.Tasks.ValueTask<TResult> `
  * `System.Threading.Tasks.Extensions`

# const

声明常量

常量是不变量,运行是不能进行任何更改,

```CSharp
const int X = 0;
```

常量只能有可识别的字面量组合, 包括`String`和`null`  
常量自己也是字面量.

# event

用于声明事件  
事件是委托的进一步包装

```CSharp
//委托
public delegate void SampleEventHandler(object sender, SampleEventArgs e);
//事件
public event SampleEventHandler SampleEvent;
```

事件是一种特殊的多播委托  
仅可以从声明的类中进行`=`操作和`调用`操作.

*事件可以自定义`Add`和`Remove`方法*

# extern

`extern` 修饰符用于声明在外部实现的方法  
这经常用于导入外部`dll`文件.  

```CSharp
[DllImport("avifil32.dll")]
private static extern void AVIFileInit();
```
*这样便可以使用`avifil32.dll`中的`AVIFileInit`方法了*

> `extern`关键字还可以用来指定`外部别名`,用于处理程序集名称相同的情况

# in(泛型修饰符)

`in` 关键字可指定泛型的类型参数是逆变的

逆变使你使用的类型可以比泛型参数指定的类型派生程度更小  
也就是说,需要一个父类,你可以传入一个子类.

但是代价则是,你只能将声明为逆变的类型用作参数,而非返回值

```CSharp
IContravariant<Object> iobj = new Sample<Object>();
IContravariant<String> istr = new Sample<String>();
istr = iobj;
//现在可以通过传入`String`来调用原本的iobj. 这不会报错,因为`T`被限制只用作参数
```

# new(成员修饰符)

在用作声明修饰符时,`new` 关键字可以显式隐藏从基类继承的成员  
可以不使用 `new` 修饰符来隐藏成员,但将收到编译器警告,编译器为你生成隐藏的`new`

```CSharp
public class BaseC
{
    public int x;
    public void Invoke() { }
}
public class DerivedC : BaseC
{
    new public void Invoke() { }
}
```
*可见, 若要隐藏不是可派生类型的父类成员,可以使用`new`声明一个同名的成员*

new 修饰符会用同样的名称创建一个新成员并使原始成员变为隐藏

# out(泛型修饰符)

`out` 关键字可指定泛型的类型参数是协变的

协变使你使用的类型可以比泛型参数指定的类型派生程度更大  
也就是说,需要一个子类,你可以传入一个父类.

但是代价则是,你只能将声明为协变的类型用作返回值,而非参数

```CSharp
IEnumerable<Object> iobj = new IEnumerable<Object>();
IEnumerable<String> istr = new IEnumerable<String>();
iobj = istr;
//现在调用`iobj`实际是在调用`istr`这不会报错,因为`T`被限制只用作返回.  所以需求`Object`返回值的泛型自然可以接受`String`类型
```

此规则有一个例外。 如果在协变接口中将逆变泛型委托用作方法参数,则可以将协变类型用作此委托的泛型类型参数

> 说实话没看懂,别搞骚操作就是了.

# override

扩展或修改继承父类的成员.

```CSharp
abstract class Shape
{
    public abstract int GetArea();
}

class Square : Shape
{
    int side;

    public Square(int n) => side = n;

    // GetArea method is required to avoid a compile-time error.
    public override int GetArea() => side * side;

    static void Main()
    {
        var sq = new Square(12);
        Console.WriteLine($"Area of the square = {sq.GetArea()}");
    }
}
// Output: Area of the square = 144
```

对于定义为`virtual`的父类成员,需要使用`override`来覆盖.  
*`abstract`实际是隐藏的`virtual`*  
*`override`本身也是隐藏的`virtual`*

*C# 9.0 开始,override 方法支持协变返回类型*

# readonly

在字段声明中,`readonly` 指示只能在声明期间或在同一个类的构造函数中向字段赋值.  

由于值类型直接包含数据,因此属于 `readonly` 值类型的字段不可变  
由于引用类型包含对其数据的引用,因此属于 readonly 引用类型的字段必须始终引用同一对象。 该对象是可变的.  
*类似于地址不变,堆内容可变*  
*由于这不安全也不符合预期,将生成一个警告*

* 在 `readonly struct` 类型定义中,`readonly` 指示结构类型是不可变的
* 在结构类型内的实例成员声明中,`readonly` 指示实例成员不修改结构的状态
* 在 `ref readonly` 方法返回中,指示该方法返回一个引用,且不允许向该引用写入内容.
  * 所返回的类型不需要为 `readonly struct`。 `ref` 能返回的任何类型都能由 `ref readonly` 返回

# sealed

应用于某个类时,`sealed`修饰符可阻止其他类继承自己  

```CSharp
class A {}
sealed class B : A {}
```
*`B`无法再次被继承*

不对类使用而对基类中的虚方法或属性的方法或属性使用 `sealed` 修饰符  
这使你可以允许类派生自你的类并防止它们替代特定虚方法或属性

```CSharp
class X
{
    protected virtual void F() { Console.WriteLine("X.F"); }
    protected virtual void F2() { Console.WriteLine("X.F2"); }
}

class Y : X
{
    sealed protected override void F() { Console.WriteLine("Y.F"); }
    protected override void F2() { Console.WriteLine("Y.F2"); }
}

class Z : Y
{
    protected override void F2() { Console.WriteLine("Z.F2"); }
	//无法重写F()
}
```

**结构是隐式密封的,所以无法继承结构**

# static

使用 `static` 修饰符可声明属于类型本身而不是属于特定对象的静态成员

拥有此声明的成员无需实例即可访问.

```CSharp
static class CompanyEmployee
{
    public static void DoSomething() { /*...*/ }
    public static void DoSomethingElse() { /*...*/  }
}
CompanyEmployee.DoSomething();//无需实例化
```
*这导致,每个 static 字段只有一个副本*

**类可以不声明为`static`,但如果声明了,则其中只能由`static`成员**  
*这种类可以用来做`扩展函数`*

*从 `C# 8.0`开始,可以将 `static` 修饰符添加到本地函数。 静态本地函数无法捕获局部变量或实例状态*

*从 `C# 9.0` 开始,可将 `static` 修饰符添加到 `Lambda` 表达式或匿名方法。 静态`Lambda`表达式或匿名方法无法捕获局部变量或实例状态*

# unsafe

`unsafe` 关键字表示不安全上下文,该上下文是任何涉及`指针`的操作所必需的

# virtual

`virtual`用于指定自己的成员可以被子类重写

```CSharp
public class A
{
`	public virtual double Area()
	{
		return x * y;
	}`
}
public class B:A
{
`	public Override double Area()
	{
		return x * y + 1;
	}`
}
```
*虚拟成员的实现可由派生类中的替代成员更改*

调用虚拟方法时,将为替代的成员检查该对象的运行时类型  
如果没有替代该成员,则它可能是原始成员

*默认情况下,方法是非虚拟的。 不能替代非虚方法*

# volatile

`volatile` 关键字指示一个字段可以由多个同时执行的线程修改。 出于性能原因,编译器,运行时系统甚至硬件都可能重新排列对存储器位置的读取和写入

*`double` 和 `long`无法标记为 `volatile`,因为对这些类型的字段的读取和写入不能保证是原子的*

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
