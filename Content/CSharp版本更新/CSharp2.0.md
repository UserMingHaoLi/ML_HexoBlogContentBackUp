---
title: CSharp2.0
date: 2021-03-29 22:52:00
updated: 2021-03-29 22:52:00
id: ml-20210329-225200-g107
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

- [泛型](#泛型)
- [分部类型](#分部类型)
- [匿名方法](#匿名方法)
- [可以为 null 的类型](#可以为-null-的类型)
- [迭代器](#迭代器)
- [协变和逆变](#协变和逆变)
- [getter/setter 单独可访问性](#gettersetter-单独可访问性)
- [方法组转换（委托）](#方法组转换委托)
- [静态类](#静态类)
  - [委托推断](#委托推断)
- [完毕](#完毕)

<!--more-->

# 泛型

泛型, 也就是我们经常看到的`T`类型这个`T`是一个约定俗成的参数名称, 实际可以随意自己命名.  
我们经常在C#类库中看到`List<T>`, 这就是一种泛型. 下面使用一下试试.

```CSharp
List<int> intList = new List<int>();
```

这样就声明了一个<Int>类型的List. 同理, 可以声明`String`类型, 或者`double`类型.  
但是他们在源码中都归属于`List<T>`类型, 这一对`<>`加上里面的参数, 就构成了泛型. 泛型可以在运行时转化为各种形态, 等下我们会谈到他的CIL支持.

下面我们自己声明一些泛型

```csharp
class Node<T> {};
interface INode<T> {};
public T GetNode<T, T2>(T2 index){};
delegate T DGetNode<T, T2>(T2 index);
```

以上代码显示了一些关键因素

* 泛型可以随意命名, 例如`T2`
* 泛型可以声明多个, 实际上之前将到的Action和Func都是例子
* 泛型可以用于声明类, 接口, 方法, 代理.

接下来我们在内部代码使用泛型.

```csharp
public T GetNode<T2>(T2 index)
{
    T result = this.NodeList[index];
    return result;
}
```

可以看出, T就是一个类型, 可以当做Class级别来使用.  
这里就引申出了一个问题, 由于C#是强类型语言, 如果不能再编译时确定类型,  
那么就无法使用你想用的类的方法.  
泛型到底是如何判断所谓的`T`到底是什么东西的呢?  
下面我们介绍泛型的约束.  

```CSharp
public T GetNode<T, T2>(T2 index) where T : class where T2 : class
{
    return null;
}
```

使用`where`关键字, 来指定T到底属于什么类型范围, 如果不使用, 默认为object  
也就是说, 你只能使用object类型的方法和数据.  
如果最后还是在里面进行强制转化过来, 那就失去了泛型的意义了.

泛型的具体约束规则如下.

| 约束                 | 说明                                                                                                                          |
| :------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| where T : struct     | 类型参数必须是值类型. 可以指定除 Nullable<T> 以外的任何值类型. 有关可以为 null 的类型的详细信息,请参阅可以为 null 的类型. |
| where T : class      | 类型参数必须是引用类型. 此约束还应用于任何类、接口、委托或数组类型.                                                         |
| where T : unmanaged  | 类型参数不能是引用类型,并且任何嵌套级别均不能包含任何引用类型成员.                                                          |
| where T : new()      | 类型参数必须具有公共无参数构造函数. 与其他约束一起使用时,new() 约束必须最后指定.                                           |
| where T : <基类名>   | 类型参数必须是指定的基类或派生自指定的基类.                                                                                  |
| where T : <接口名称> | 类型参数必须是指定的接口或实现指定的接口. 可指定多个接口约束. 约束接口也可以是泛型.                                        |
| where T : U          | 为 T 提供的类型参数必须是为 U 提供的参数或派生自为 U 提供的参数.                                                             |

某些约束是互斥的. 所有值类型必须具有可访问的无参数构造函数. `struct` 约束包含 `new()` 约束,且 `new()` 约束不能与 `struct` 约束结合使用. `unmanaged` 约束包含 `struct` 约束. `unmanaged` 约束不能与 `struct` 或 `new()` 约束结合使用.

> unmanaged 是C#7.2新增的.

> 运行时可以通过反射获取泛型具体是什么.

> 由于无法确定其类型, 所以泛型重载是以参数个数判断的.

接下来我们说下反射的CIL实现.

当泛型指定的是值类型的时候, CIL直接创建一个对应的类型, 每次都会创建一个.  
也就是说, 当你使用`List<int>` `List<dobule>` 实际上CIL就将原本的T都替换为这个类型, 之后连续创建了两个新的类.  
其中一个是支持`int`另一个支持`double`

但是引用类型不同.  
考虑到性能优化, 仅当你使用第一引用类型的时候, CIL才会初始化一个引用类型的泛型.  
之后无论使用改泛型的任何版本, 只要是引用类型, 都是使用的此类型, 也就是说, 引用类型的泛型是唯一的.  

> 产生这种行为的原因其实是因为堆和栈, 由于值类型栈上空间不同, 所以干脆创建多个,  
但是引用类型的栈上空间是相同的, 都是一个引用而已, 所以只需要替换引用地址, 即可达成更换引用泛型的目的.  
最终结果其实还是因为CIL直接面对内存, 使用指针实现这种操作.

> 可知泛型是使用时候生成的, 也就是`JIT`, 但类似iOS平台, 或il2cpp编译都仅支持`AOT`, 也就是对于泛型支持度不高, 通常都需要特殊处理, 常见的有提前声明, 解释执行, 补充元数据

# 分部类型

使用关键字`partial`可以用于定义分布的类, 接口, 方法.  
他允许你在两个不同的文件中使用同一个类, 接口, 方法.  

一般用于  
处理大型项目时,使一个类分布于多个独立文件中可以让多位程序员同时对该类进行处理.  
使用自动生成的源文件时, 例如Windows 窗体  
Web服务包装器代码等.你可以创建使用这些类的代码,这样就不需要修改由Visual Studio生成的文件.

> 如果将任意部分声明为抽象的,则整个类型都被视为抽象的. 如果将任意部分声明为密封的,则整个类型都被视为密封的. 如果任意部分声明基类型,则整个类型都将继承该类.

> 各个部分可以指定不同的基接口,最终类型将实现所有分部声明所列出的全部接口.

```csharp
public partial class Employee
{
    public void DoWork()
    {
    }
}

public partial class Employee
{
    public void GoToLunch()
    {
    }
}
```

```CSharp
// Definition in file1.cs
partial void OnNameChanged();

// Implementation in file2.cs
partial void OnNameChanged()
{
  // method body
}
```

上面两段代码表示了如何显示使用分布类和分布方法.

> 分布方法来自C#3.0

# 匿名方法

在1.0, 声明委托只能使用完全限定模式, 现在可以使用匿名方法了.而在3.0后, 可以使用`Lambda`表达式.

```csharp
//C# 1.0的方式 M是一个声明好的方法
TestDelegate testDelA = new TestDelegate(M);

//C# 2.0 匿名方法
TestDelegate testDelB = delegate(string s) { Console.WriteLine(s); };
```

> 创建委托时,对外部变量的引用被视为已捕获   不同于本地变量,已捕获的变量的生存期一直延伸至引用匿名方法的委托具有垃圾回收资格为止.

看一个例子

```CSharp
 List<Action> actions = new List<Action>();
for (int i = 0; i < 5; i++)
{
    Action item = delegate () { Console.WriteLine(i); };
    actions.Add(item);
}
foreach (var item in actions)
{
    item.Invoke();
}
```

最后输出的结果全是`5`, 这是由于i变量只有一个, 而且生命被延续到持有它的匿名函数结束为止,  
于是等到延时调用匿名函数时, i已经累加完毕了, 输出自然是最大的数.

解决办法是在匿名函数内部声明一个变量, 立刻存储一次.

```CSharp
for (int i = 0; i < 5; i++)
{
    int j = i;
```

> 这种现象叫做`闭包`

# 可以为 null 的类型

这是由于值类型不能为`Null`但是值类型本身就是引用类型引发的一个新玩意,  
据微软说是为了解决数据库的数据没有的时候不好定义初始值用的.  

```
int? x = 10;
int? y = null
```

使用也很简单, 就是值类型后面带上一个`?`即可, 这样就声明了一个可以为`null`的值类型.  
它具有一些特殊的操作

* `x.HasValue`获取一个表示其是否为`null`的`bool`值
* `x.Value`获取它的值, 可能为`null`
* `x.GetValueOrDefault()`获取它的值, 如果为`null`,返回该值类型的默认值.
* 你可以使用`==`, `!=`来比较可空类型.

> 你可以使用`??`操作符来跳过`null`, 自己指定一个数据

```
return x ?? 10
```

这样, 如果 x 为`null`那么就返回后面的数. 可以连续跟`x ?? y ?? z ?? 10`

> 下面讲一下它的原理

实际上可空类型是一个泛型 ` System.Nullable<T>`结构的实例  
例如声明 `int?` 的时候, 实际是声明了一个 `System.Nullable<int>`  
基础类型 T 可以是任何不可为 null 的值类型. T 不能是引用类型.

> 不得嵌套可以为 null 的类型. 不会编译下面的一行代码:Nullable<Nullable<int>> n;

如个使用运算符, 其中任何一方为`null`结果为null

```
int? a = 10;
int? b = null;
int? c = 10;

a++;        // a is 11.
a = a * c;  // a is 110.
a = a + b;  // a is null.
```

如果使用比较运算符的有一方为null, 那么结果 既不大于等于 null,也不小于 null.

```
int? num1 = 10;
int? num2 = null;
if (num1 >= num2)

if (num1 < num2)

```

结果都是`false`

对于 bool? 的比较, 有下表

| x     | y     | x且y  | x或y  |
| ----- | ----- | ----- | ----- |
| true  | true  | true  | true  |
| true  | False | false | true  |
| true  | null  | null  | true  |
| False | true  | False | true  |
| False | False | False | False |
| False | null  | False | null  |
| null  | true  | null  | true  |
| null  | False | False | null  |
| null  | null  | null  | null  |

其原理是  
且, 如果有两个true, 为true. 如果有false, 为false. 如果有null和true, 为null  
或, 如果有两个false, 为false, 如果有true, 为true, 如果有null和false, 为null

> 实际不应该在未确定null值的时候使用可空类型做运算, 对于其他程序员会对结果产生困惑

# 迭代器

迭代器在1.0版本就有, 当时是两个接口` IEnumerable` `IEnumerator `  
由于加入了泛型, 这么重要的接口当然受到了第一时间的泛型支持, `IEnumerable<T>` `IEnumerator<T>`  

然后引入关键字, `yield`,  
拥有这个关键字后, 我们使用迭代器就不必实现接口了, 直接`yield`, 由编辑器帮我们转化为接口.

```csharp
static void Main()
{
    foreach (int number in SomeNumbers())
    {
        Console.Write(number.ToString() + " ");
    }
    // Output: 3 5 8
    Console.ReadKey();
}

public static System.Collections.IEnumerable SomeNumbers()
{
    yield return 3;
    yield return 5;
    yield return 8;
}
```

> 可以使用 `yield break` 语句来终止迭代.

yield的原理是运行到一个`yield return`后就停下, 等待下一次运行. 直到结束.  
这样就形成了一个集合, 于是可以被转化为迭代器, 实际上就是在模拟`MoveNext()`

>注意 迭代器没有Reset(). 

# 协变和逆变

这玩意看的头痛, 得上手做才懂.

可以使用`out`关键字声明参数参与协变.
`in`关键字是逆变

网上的例子一般以`IEnumerable<out T>` 和 `Action<in T>`讲解, 致使不了解的朋友们听不明白, 本例子会使用玩家自主类来讲解, 希望你们听懂.

首先明确概念, 协变和逆变是用于解决泛型的隐式转化用的,   
因为我们知道, 泛型对于我们程序员来说, 实际上每一个都是单独的类型, 不能向父类转化的.

然后我们仿照 `IEnumerable<out T>` 来写一个接口和一个类型.

```CSharp
interface IOut_Interface<out T>
{
    T GetR();
}

class COut_Class<T> : IOut_Interface<T>
{
    public T GetR()
    {
        return default(T);
    }
}
```

这样就声明了一个协变的接口和一个实现该接口的类.  
此时, 我们可以尝试添加T为参数

```CSharp
T GetR(T t);//error
```

==报错了, 错误信息是==

> 类型参数"T"必须是在"Program.IOut_Interface<T>.GetR(T)"上有效的逆变式."T"为 协变

可见我们已经成功声明了协变, 之后声明一个逆变.

```CSharp
interface IIn_Interface<U>
{
    void SetU(U u);
}
class CIn_Class<U> : IIn_Interface<U>
{
    public void SetU(U u)
    {
        Console.WriteLine(typeof(U));
    }
}
```

==老规矩. 将接口返回的`void` 修改为`U`==

>  类型参数"U"必须是在"Program.IIn_Interface<U>.SetU(U)"上有效的 协变式."U"为 逆变.

准备工作完毕后, 我们准备写`Main`

```csharp
static void Main(string[] args)
{
    IOut_Interface<object> c1 = new COut_Class<object>();
    IOut_Interface<string> c12 = new COut_Class<string>();
    c1 = c12;
   // c12 = c1; error

    IIn_Interface<object> c2 = new CIn_Class<object>();
    IIn_Interface<string> c21 = new CIn_Class<string>();
    c21 = c2;
   // c2 = c21; error
 
    Console.Read();
}
```

先看前半部分,  我们分别声明了`<object>`和`<string>`类型, 并存放在接口中.  
我们知道. 此接口只有返回, 没有参数, 也就是说,  
两个类的方法, 分别会返回`string`和`object`类型, 那么理所当然,  
可以将`string`赋值给`object`  
但是真的是理所当然吗?  

我们尝试删除接口的`out`关键字.

> 无法将类型"HelloWorldTests.Program.IOut_Interface<string>"隐式转换为"HelloWorldTests.Program.IOut_Interface<object>".存在一个显式转换(是否缺少强制转换?)

并不是. 这是为什么呢, 这就关系到一个类型安全问题.  
因为泛型并没有约束你用它做任何事, 你可以将它做参数, 也可以做返回值, 甚至什么也不做. 但是一旦你使用它做参数, 那么`c1 = c12`还正确吗?  

本来接口要求传入`string`参数, 你传入一个`object`满足吗?  

`out`关键字协变, 就是要求你不能将泛型用作参数. 用于保证安全.  
其实和约束类似了.  

那么, 聪明的朋友可能已经推断除了`in`关键字的用途了吧,  
`in`就是约束你不能将泛型用作返回值. 用于保证一个参数的安全.

于是你可以看到, 代码的下半部分实际上是将一个`object`的泛型赋值给了`string`泛型.  这是因为在参数上, `string`完全可以代替`object`.  

> 如果你不明白继承, 希望你继续学习C#基础.

> 你可以在一个接口中定义多个泛型, 随意指定他们的`out` `in`, 只要你遵守协议即可以正常运行.

# getter/setter 单独可访问性

也就是属性中的`get` `set`可以单独指定访问修饰符

```csharp
public string FirstName
    {
        get 
        {
            return firstName
        }
        protected set
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("First name must not be blank");
            firstName = value;
        }
    }
```

可以用于只公布一个对外参数.

由于定义`FirstName`时, 指定了其访问修饰符为`public`,默认get,set都是这个级别

所以不能定义两个方法都为其他级别, 只能定义一个或者都不定义.

> 实际上是封装了两个方法, 性能及其敏感场合还是用字段

# 方法组转换（委托）

实际上就是使用一个方法, 但不使用其`()`  
用于为委托赋值和委托推断, 实际上1.0的委托是需要声明的, 2.0只需要赋值.  
类似语法糖吧,  

```csharp
//C# 1.0中创建委托实例
ReverseStringHandler reverseStringHandler = new ReverseStringHandler(ReverseString);

//C# 2.0中通过方法组转换创建委托实例
reverseStringHandler = ReverseString;
```

> 如果你在错误列表中发现有关方法组的信息, 检查你的小括号.

# 静态类

使用`static`修饰类, 如此修饰过的类中存在的任何都必须是静态的.  

```CSharp
static class C1 {}
```

声明为静态类的方法不能拥有`New`构造器, 但是可以拥有静态的构造器.  
只需要在传统构造器中加入`static`关键字即可.  
如果你没写, 编辑器帮你.  

实际上, 在任何类中书写的即刻赋值, 都会被移动到构造器中进行, 无论是实例构造还是静态构造.  

```CSharp
int a = 1;  //移动到实例构造
static int b = 2;   //移动到静态构造.
```

> 会移动到前排,所以会被真正构造器的代码覆盖

按照微软的说法, 只有在你使用的时候才会进行静态初始化, 经过我测试,  
实则不然.  
先上结论
> 如果你不声明构造函数转而由编译器声明, 那么一旦载入程序, 立刻就会初始化  
如果你自己写构造, 只有在使用的时候初始化.  
如果你既在构造函数写了也在外面声明的时候写了,在使用时候占用双倍内存初始化.

下面是代码实测.

> `Process.GetCurrentProcess().WorkingSet64` 是当前进程内存占用量.

首先声明一个自动初始化的类

```csharp
static class sc
{
    public static char[] cs = new char[204800000];
}
```
然后是`Main`
```csharp
string rl = "";
do
{
    rl = Console.ReadLine();
    if (rl == "cw") { var mcs = sc.cs; }
    Console.WriteLine(System.Diagnostics.Process.GetCurrentProcess().WorkingSet64);
} while (true);
```

看下结果

```
a
15450112
a
15921152
cw
15953920
cw
15986688
a
16011264
a
16035840
```

可以看到没有明显增幅.  
然后看下手动初始化

```csharp
public static char[] cs;
static sc()
{
    cs = new char[204800000];
}
```
结果
```
a
14385152
a
14843904
cw
15724544
cw
15757312
a
15781888
a
15806464
```

可以看到有明显增幅, 确定是在调用的时候进行了初始化,   
而且初始化后的数字也与上一个自动的相当.  
最后看一个里外都赋值的

```csharp
static class sc
{
    public static char[] cs = new char[204800000];
    static sc()
    {
        cs = new char[204800000];
    }
}
```
```
a
14397440
a
14876672
cw
16781312
cw
16789504
a
16793600
a
16818176
```

可以明显看出进行了双倍初始化. 而且短时间不会被释放.  
我们进行一次GC试试.  
添加代码
```csharp
Console.WriteLine(System.Diagnostics.Process.GetCurrentProcess().WorkingSet64);
    if (rl == "cw") { GC.Collect(); }
} while (true);
```
```
a
14442496
a
14921728
cw
16830464
cw
16441344
a
16465920
a
16490496
```
可以看到确实是清理了垃圾资源, 但是还是没有回到单次初始化的水平,  
推测是GC本身第一次运行初始化占用了资源.

## 委托推断

和方法组一个类别, 基于方法组实现的自动推断方法类型是否符合, 如果符合,  
可以直接赋值方法给委托, 不必用委托再包装一遍.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
