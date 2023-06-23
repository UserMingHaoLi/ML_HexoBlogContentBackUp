---
title: CSharp1.0
date: 2021-03-29 22:11:00
updated: 2021-03-29 22:11:00
id: ml-20210329-221100-g106
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

# C# 1.0

>本系列均参考自
>>[微软官方文档][MicrosoftDoc]

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

发布日期:2002 年 1 月

<!--more-->

# C#1.0是C#最初始的版本

由于创建时间较晚, 良好的吸取了前人经验, 拥有面向对象架构的同时拥有一些自己独特的东西. 
如果只是想入门C# 可以参阅 [微软官方C#指南][AddLink1]  
本文提供的是C#历史变更记录.

# 面向对象基本特性 -**封装**, **继承**, **多态**

任何一门现代面向对象语言都会有的基本能力

## **封装**

提供对实现细节的隐藏

具体实现可以参考[MSDN访问修饰符(C#参考)][link1]

简单讲一下大概就是以下四个关键字

```CSharp
public //公开的
protected //仅继承的
internal //仅当前程序集的
private //私有的
```

但又有两组关键字组合的访问修饰符

```CSharp
protected internal  //在程序集中和继承中都可访问
private protected   //C#7.2新增 在继承类中访问私有成员
```

## **继承**

在类的声明是使用`:`来进行继承类或者实现接口, 也可以继续跟`,`来继续实现接口  
如下

```CSharp
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
## **多态**

多态可以说是面向对象编程的关键, 非一日之功, 不是一时半会可以讲清楚的.  
我只说一个大概吧  
继续刚刚的类, 我们写一个Main
```CSharp
public void Main()
{
    Base m_base = new Son();    //声明一个父类, 实际里面装的子类
    ISon m_Son = new Son();     //声明一个接口, 里面装的一个实现接口的类
    //调用的时候内容以声明的为准.
}
```

以上就是一个最简单的多态.  
更细的资料可以 [百度][link2] OR [Google][link3]

# 数据类型 -**值类型**, **引用类型**

数据就是程序的根本, 任何语言都应该有数据类型 哪怕是`var`  

C# 充分吸取教训, 既有常用的值类型, 也有更灵活的引用类型, 还支持自定义值类型`struct`

## **值类型**

可以参阅[MSDN 值类型][link4] 内有值类型相关详细定义和关键字说明

这里只讲值类型的根本和实际操作中的易忽略项.

例如: 值类型的深拷贝

>所有值类型的赋值, 实际上都是将自己完全复制一份, 也就是说 从一份变成了两份

```CSharp
int a = 1;
int b = a;
b = 3;
print(a)
print(b)
```

输出

```CSharp
1
3
```

## **引用类型**

引用类型与值类型最大的不同就是浅拷贝

当声明一个引用类型的时候

```CSharp
M_Class A = new M_Class();
```

此时 A并没有装下一个M_Class, 而是装下了一个M_Class的地址  

此时重复之前值类型的操作试试.

```CSharp
M_Class A = new M_Class();
M_Class B = A;
B.num = 3;
print(B.num)
print(B.num)
```

没错! 都是`3`

这是因为`A` 和 `B` 都拥有同一个地址.

当声明一个 `A` 的时候, 实际上是在 栈上分配了一个空间(大小根据操作系统而定, 但都很小)

里面实际上只存储了一小串堆的地址  
大概类似于 `FABC11` 这种  
这串地址在计算机中意味着一段新内存, 计算机通过解析这段地址到达一个新的地方, 也就是

`new M_Class()` 做的事情, 他在堆上分配了一个大空间, 大到足以容纳整个`M_Class`

我们只是通过A来查找之前 `new` 出来的M_Class而已.

所以, 当`B=A` 实际B的内容和A一样 都是 `FABC11` 所以他们都指向同一个 `M_Class` .  
当B修改, 最后A访问的时候, 得到的是修改后的值.

## **装箱和拆箱**

之前提到了 
>所有值类型都派生自`Object.System.ValueType` 

但是我们知道, `Object` 是一个引用类型, 其子类也是引用类型, 那为什么会有值类型呢.

这就是微软变得一个戏法了.

实际上可以通过向父类转型来将一个值类型变成一个引用类型

```CSharp
int a = 1
Object obj = (Object)a
```

这样 就将原本的值类型包装成了一个引用类型, 这就叫装箱.

相反的

```CSharp
a = (int)obj
```
这是拆箱

其中 在一个变量前使用(int)或者是(Object)或者是其他任何类型(?), 这是一种C#语法, 叫做强制转换. 可以将一个类型强制转换为另一个类型.

*由于是程序员强制要求的,所以编译器不会再做检查. 可能导致严重错误.*

另外 在以前 装箱和拆箱都是严重的性能消耗.

还可以通过`ICloneable`接口来自定义拷贝. 

# 集合

大概就是 List Array Queue Stack Dictionary 这种吧.

是大量同类型数据的集, 并提供了一些读取,写入, 群体操作的基本方法. 配合`Linq`还是很好玩的.

参阅[微软 C#集合][link6]


# 迭代器

迭代器其实算是集合的一部分, 不过它太关键了, 还是拉出来讲.
所谓迭代器, 其实是两个接口 `IEnumerable` 和 `IEnumerator`

实际 `IEnumerable` 要求返回一个 `IEnumerator` 对象, 也就是说, 底层还是`IEnumerator`

来看看他们的源码

```CSharp
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

实际的所谓迭代器, 就是通过`MoveNext()`之后使用`Current`遍历全部元素而已, 也可以继承这些接口, 实现自己的迭代器.

 `foreach` 关键字正是使用了迭代器进行遍历.

还有一个关键因素, Current只有Get属性, 也就是说, 不可能在迭代器内修改值类型.  
实际上, 不推荐在迭代器内修改任何数据, 可以使用标志法, 即先给物体打上一个标记, 可以使用另一个集合保存被标记数据的位置, 等遍历结束后, 再进行操作.这样是比较正规的做法.


# 消息机制, 事件与代理

实际上就是 `delegate` 关键字和 `event` 关键字

windows的基调就是回调, 消息, 通知, 这些全都由以上两个关键字全权负责了.

`delegate` 实际上是一个函数的指针, 

持有一个函数的地址可以干什么呢? 这就是回调函数, 持有一个函数, 就可以在任何时间点调用它, 使程序具有传递性和延时性.

还有消息和通知, 其实就是将函数注册到其他的地方, 由其他方法回调, 这样就形成了订阅, 通知模式, 这涉及到一种设计模式 `观察者模式`


# 多线程

不讲.

# Attribute 特性

实际上就是在类或者方法前面加上一个标签, 这样他们就有额外的元数据, 在编译时候可以进行一些基于这些额外元数据的操作.

```CSharp
[TestAttribute]
public struct TestStruct { }
```

这个 `TestAttribute` 就是自定义的Attribute, 也可以使用以下C#自带的.

[`Obsolete`] 　
声明一个函数被废弃, 之后会在控制台输出错误信息.

[`Conditional(DEBUG)`]
声明一个函数只能运行在`DEBUG`宏模式下. 可以更改()内的内容兼容其他宏

# 自动垃圾回收

其实也就是大名鼎鼎的 `GC` GC它是全自动的, 也可以手动调用 `GC.Collect();` 强制进行, 一般我们并不会手动调用.

GC相关还有一个接口 `IDisposible` 他的同胞兄弟是 关键字 `using` 区块

```CSharp
using(File.Opne("XXX"))
{
    //使用完毕之后自动调用 IDisposible 接口
}
```

还有一个 `~` 构析函数, 与构造函数对应, 垃圾回收会调用这些函数

# 反射和动态创建

是一个运行时动态创建类或者修改数据的东西  
一般可以用于解析配置文件来改变程序状态．
我一般用于处理棘手的数据处理问题, 之后应当重构.

> 也可以用于解析特性`[Attr]`

# 类库

不必多言, 就是库.

> 自此 C# 1.0 就此完结  
> 最后,所有C#程序员都应该看下CIL  
> [C#源码地址][CSharpReferencesource]

# C#1.2增补

随 Visual Studio 2003 一起提供的 C# 版本 1.2. 它对语言做了一些小改进. 最值得注意的是,从此版本开始,当 `IEnumerator` 实现 `IDisposable` 时,`foreach` 循环中生成的代码会在 `IEnumerator` 上调用 `Dispose`.

# 1.0基础入门详解.

上面讲的太深了, 这个偏基础一点

## 类

说到类就要说名词空间  
先看一个基本的C#项目结构

```CSharp
namespace HelloWorldTests.Tests
{
    public class ProgramTests
    {
        public void Main()
        {
            return;
        }
    }
}
```

其中 `namespace` 关键字标属的是名词空间.  
`class`是类关键字, 类里面的Main这是方法名称.  
其中 `public`关键字是访问级别, 表示公开的.  
此时, 我们就声明了一个完整的类, 然后我们使用它.  

```CSharp
ProgramTests mc = New ProgramTests();
mc.Main();
```

第一句声明了一个`ProgramTests`类的栈上引用`mc`和一个堆上空间`new`  
将堆的地址赋值给`mc`, 此时`mc`存储有`New ProgramTests()`的所在地址.  
于是可以通过`mc.Main()`来调用这个`ProgramTests`类的方法.  
再看一个例子

```CSharp
ProgramTests mc
```

如果只声明了栈空间, 那么它暂时不指向任何堆空间, 此时使用它会引发异常.

>注意: 如果一个方法名称和类名称相同, 那么就属于构造方法, 不需要返回值, 可以使用`new`关键字来调用, 如果没有, 编辑器会默认生成一个`空`的构造方法.

## 结构

结构和类的声明相似, 但是运行方式却截然不同.

```csharp
public struct MyStruct
{
    //结构内的数据
    public int x, y;

    public Coords(int p1, int p2)
    {
        x = p1;
        y = p2;
    }
}

public void Main()
{
    MyStruct m1 = new Coords(); //此时 x=y=0 因为0是int的默认值
    MyStruct m1 = new Coords(10, 10);

}
```

只是将`class`替换为了`struct`但是内部机理却从引用类型变成了值类型.

* 在结构声明中,除非将字段声明为 `const` 或 `static`,否则无法初始化.
* 结构不能声明默认构造函数（没有参数的构造函数）或终结器(`~`).
* 结构在分配时进行复制(深拷贝). 
* 将结构分配给新变量时,将复制所有数据,并且对新副本所做的任何修改不会更改原始副本的数据.在处理值类型的集合（如 Dictionary<string, myStruct>）时,请务必记住这一点.
* 结构是值类型,不同于类,类是引用类型.
* 与类不同,无需使用 new 运算符即可对结构进行实例化(此时使用结构内所有数据自带的默认值).
* 结构可以声明具有参数的构造函数.
* 一个结构无法继承自另一个结构或类,并且它不能为类的基类. 所有结构都直接继承自 ValueType,后者继承自Object.
* 结构可以实现接口.
* 结构不能为 null,并且不能向结构变量分配 null,除非将变量声明为可为 null 的类型.

## 接口

用于定义类或者结构需要实现的功能描述, 不具体实现某个功能.  
接口是实现多态的重要功能, 是C#程序员, 乃至所有面向对象程序员必须要学习理解的概念.  
由于C#不支持类的多继承,单支持接口的多实现, 所以接口又显得格外重要.

使用`interface`关键字定义一组接口.

```csharp
interface IInterFace
{
    int Add(int a, int b);
}
```

* 由于接口是约定好的功能实现描述, 所以接口, 和接口内地方法都是默认public类型的, 无需手动指定修饰符.
* 接口只负责显示约定, 不负责具体实现
* 接口名称一般以`I`开头, 之后的名字也遵循大驼峰写法.
* 如果其他类或者接口要实现本接口, 那么他们的类或者结构中必然要有接口内地全部方法.
* 接口可以包含方法、属性、事件、索引器或这四种成员类型的任意组合

下面演示一个继承接口的实现

```csharp
public class MyNumber : IInterFace
{
    public int Add(int x, int y)
    {
        return x + y;
    }
}
```

由于`MyNumber`实现`IInterFace`, 所以他必须有`Add`方法.  
基于这一条, 可以实现某种多态

```csharp
IInterFace uadd = new MyNumber()
uadd.Add(1,2)
```

可以声明一个接口, 但是其内部是实现过改接口的其他类. 这样就遮蔽了其他实现, 只留下接口想要的方法, 达成多态.  

>注意: 接口的实现可能违背意愿, 如下

```csharp
public class MyNumber : IInterFace
{
    public int Add(int x, int y)
    {
        return x - y;
    }
}
```

此时 `Add`方法并不返回相加后的结果, 而是进行了减法, 但是它完全符合接口定义,  
相同的返回值`int`  
相同的方法名称`Add`  
相同的参数`(int,int)`  
但是这并不是接口想要的实现, 所以接口只是一个规定, 具体实现还得看人.

## 事件

其实应该先讲委托, 因为事件基于委托. 观看此部分默认你已经先观看过委托了.

实际上, 事件只是在声明委托实例的时候, 加上一个`event`关键字

```CSharp
public delegate int Add(int a, int b);
public event Add MyAdd;
```

此时完成了一个事件的声明,  
事件与委托的最大区别在于, 只允许在事件声明的类中使用`=`为事件赋值,  
在声明类之外的地方只允许使用`+=`和`-=`  
这样就保证了事件不会在外界被莫名其妙的消失掉了.  
也就是说事件实际上是委托的封装.  

```CSharp
event EventHandler<SearchDirectoryArgs> DirectoryChanged
{
    add { directoryChanged += value; }
    remove { directoryChanged -= value; }
}
```

这个是完整的声明, 其中add对应`+=`, remove对应`-=`.

## 属性

属性是字段的延伸, 实际本质是方法, 但是C#为我们准备了语法糖.

```CSharp
public string FirstName { get; set; }
```

只需要在字段后面移除`;`在加上一对大括号, 内书`get` `set`. 即可让编辑器帮你自动生成属性.  
实际上, get和set中还可以填写相关逻辑  

```csharp
public string FirstName 
{ 
    get
    {
        return firstName;
    }
    set
    {
        firstname = value
    }
}
```

这就表明了属性实际上是两个方法的本质.  
实际你使用 `string myname = FirstName` 或者 `FirstName = "nic"` 这时候就是在调用 get, set方法.  
你可能注意到了`set`方法里面的`value`, 这是由于, set方法实际上没有定义参数, 但是赋值会带来一个参数,  
value就是这个作用, 他是一个上下文关键字, 只在这里的时候作为关键字使用, 表示参数.

下面做一个简单的访问控制  

```CSharp
public string FirstName
    {
        get 
        {
            return firstName
        }
        set
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("First name must not be blank");
            firstName = value;
        }
    }
```

自此, 属性演示完毕.

## 委托

使用委托, 你需要`delegate`关键字, 他映射的是`MulticastDelegate`类, 这个类是从`Delegate`类中派生出来的.  
但是你并不能从代码中直接访问他们, 你只能使用`delegate`关键字, 由编辑器为你创建委托.  
这保证了委托的安全性, 下面看一个创建委托的例子.

```csharp
public delegate int Add(int a, int b);
```

看起来很像是一个方法声明是吧, 只是在`public`后面 返回值前面添加了一个`delegate`关键字而已.  
但是这里声明的并不是一个方法, 而是一个委托,  
委托和方法不同级别, 委托实际上是一个继承自`MulticastDelegate`类的类型. 也就是`class`  
自然是类型, 那么除了有类型本身, 还应该有相对应的实例, 我们下面声明一个刚刚的委托实例.  

```CSharp
public Add MyAdd;
```

没错, 这就声明完成了, 看起来似乎是一个`null`值, 实际上的确是一个`null`直接使用会造成异常.  
虽然委托是一个类型, 但是我们并不需要`new`一个委托来赋值给他.  
在讲为委托赋值之前, 我们先看下委托存在的意义,和用法.

委托, 顾名思义, 是指将自己的事情交给其他人来做.  
实际上, 委托与接口类似, 声明了一个办事基准, 只要是符合这个基准的方法都交给委托来执行.  
委托常用以参数形式传递方法, 之后执行`回调函数`或者是传递`事件`.  

那么, 我们究竟该如何为委托赋值呢.  
观看委托的声明, 我们发现委托基本就是一个方法的声明格式,  实际上, 委托的确是需要接收符合该格式的方法.  

```csharp
public int Addint(int x, int y)
{
    return x + y;
}

MyAdd = Addint
```

> 注意, 此处是将方法本体赋值给了委托, 不需要后面的小括号, 如果使用了, 小括号, 就会调用方法, 返回一个int出来, 就不符合委托的需求了.

此时, MyAdd里面就存储了一个和他定义一致的方法, 之后, 可以通过调用MyAdd来调用Addint, 以下两种方法皆可.

```CSharp
MyAdd.Invoke();
MyAdd();
```

那么, 我们在来提现一下传递参数的特性吧.

```CSharp
public void TestCallBack(Add delegateAdd)
{
    delegateAdd();
}
TestCallBack(MyAdd);
```

此时, 我们就将一个方法化成一个参数进行了传递, 而之后这个方法何时调用则由方法内部决定了.  
实际上, 可以不用传递实际的委托, 而是直接传递一个方法, 如果该方法符合这个参数的标准, 会自动转化为一个同类型委托.

```CSharp
TestCallBack(Addint);
```

实际上系统默认委托足够90%状态下使用了.  
` Action<T1,T2,T3,T4,T5,T6,T7,T8,T9,T10,T11,T12,T13,T14,T15,T16>`, 最多支持 16个泛型参数, 最少0个.  
` Func<T1,T2,T3,T4,T5,T6,T7,T8,T9,T10,T11,T12,T13,T14,T15,T16,TResult>`, 最多支持15个泛型参数和1个返回值, 最少0个参数和1个返回值  
这些委托都是系统定义好的, 你需要使用的时候只需要

```CSharp
Func<int, int, int> myfunc
myfunc = Addint
```

省去了一步`delegate`定义

实际还有一个系统委托 `bool Predicate<T>` 传入一个参数, 返回一个bool,  
但是他其实可以被`Func`完全取代.  

> 注意, 实际上1.0的版本中,是没有泛型的, 也就是说, 上面所有的`T`都不存在. 请在至少2.0版本中实验.

> 注意 1.0版本的委托还是需要new的, 在2.0之后才能直接将方法赋值给委托.

### 多播委托

关键字看起来高大上, 实际上就是指一个委托可以累加复制, 形成一个委托引用多个方法.  
使用`+=`关键字, 相应的,减少也要使用`-=`关键字.  

```
MyAdd += Addint
MyAdd += Addint2
MyAdd += Addint3
```
此时调用`MyAdd()`则会依次调用添加进去的所有方法.  
如果中途不小心调用了一次`=`, 那么逻辑就会和你预想的有差异, 而且极其不好排查错误, 毕竟只是一个符号差距.  
于是就引申出了事件.

# 继续深入

> 待续

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[MicrosoftDoc]:https://docs.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-version-history "微软官方文档"


[link1]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/access-modifiers "C#访问修饰符 -封装"

[link2]: https://www.baidu.com "百度一下"

[link3]: https://www.google.com "谷歌官网 需要FQ"

[link4]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/value-types "C# 值类型"

[link5]: https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/struct "用户自定义值类型 struct"

[link6]: https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/collections "集合"

[AddLink1]: https://docs.microsoft.com/zh-cn/dotnet/csharp/index "C#指南"

[AddLink2]: https://docs.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-version-history "C#历史"

[CSharpReferencesource]:https://referencesource.microsoft.com/ "微软官方 C#源码"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
