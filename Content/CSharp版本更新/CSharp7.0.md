---
title: CSharp7.0
date: 2021-04-04 23:32:00
updated: 2021-04-04 23:32:00
id: ml-20210404-233200-g112
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

实际是包含了7.0到7.3

- [C#7.0](#c70)
  - [`out`变量](#out变量)
  - [元组](#元组)
    - [自定义元组Item名称](#自定义元组item名称)
  - [弃元](#弃元)
  - [模式匹配](#模式匹配)
  - [本地函数](#本地函数)
  - [更多 expression-bodied 成员](#更多-expression-bodied-成员)
  - [`ref`局部变量](#ref局部变量)
  - [`throw`表达式](#throw表达式)
  - [通用的异步返回类型](#通用的异步返回类型)
  - [数字文本语法改进](#数字文本语法改进)
- [C#7.1](#c71)
  - [`async``Main`方法](#asyncmain方法)
  - [`default`方法](#default方法)
  - [推断元组元素名称](#推断元组元素名称)
  - [泛型类型参数的模式匹配](#泛型类型参数的模式匹配)
- [C# 7.2](#c-72)
  - [非尾随命名方式](#非尾随命名方式)
  - [数值文字中的前导下划线](#数值文字中的前导下划线)
  - [private protected 修饰符](#private-protected-修饰符)
  - [条件ref表达式](#条件ref表达式)
- [C# 7.3](#c-73)
  - [增强的泛型约束](#增强的泛型约束)
  - [元组的`==` `!=`](#元组的-)
  - [in 参数修饰符](#in-参数修饰符)
- [完毕](#完毕)

<!--more-->

# C#7.0

## `out`变量

以前声明`out`变量的时候, 你需要先声明一个变量, 再使用`out`来修饰它.

```C#
int num
if(int.TryParse(input, out num))
```

现在, C#允许你在使用`out`修饰的时候直接声明变量.

```C#
if(int.TryParse(input, out int num))
```

别忘了, C#支持隐式变量.

```C#
if(int.TryParse(input, out var num))
```

这并不意味着`num`在`if`内, 其实是一个语法糖, 它与`if`是同级别的.  
> 可以看作是在if上方有一个语句`var num;`

*有时候并不是在上方,但任然是同级的,如下的`out var j`*


```C#
public class B
{
   public B(int i, out int j)
   {
      j = i;
   }
}

public class D : B
{
   public D(int i) : base(i, out var j)
   {
      Console.WriteLine($"The value of 'j' is {j}");
   }
}
```

## 元组

元组是包含多个字段以表示数据成员的轻量级数据结构。 这些字段没有经过验证，并且你无法定义自己的方法

使用`()`创建一个元组

```C#
var let = ("a", "b");
```

此元组默认生成两个元素, Item1和Item2.  
你也可以显示声明元组

```C#
(string Alpha, string Beta) namedLetters = ("a", "b");
Console.WriteLine($"{namedLetters.Alpha}, {namedLetters.Beta}");
```

或者你可以从右侧指定名称

```C#
var alphabetStart = (Alpha: "a", Beta: "b");
Console.WriteLine($"{alphabetStart.Alpha}, {alphabetStart.Beta}");
```

*如果同时指定, 会得到一个警告, 右侧命名将会失效*

元组一般用于方法的返回值, 但最好是`private`或者是`internal`的, 因为那是你自己的东西.  
公开方法最好是使用定义好的`class`和`struct`  
下面演示一个方法

```C#
private static (int Max, int Min) Range(Ienumerable<int> num)
{
    int min = int.MinValue;
    int max = int.MaxValue;
    return (max, min)
}
```

有时候不希望使用`.`来取得数据,那么可以使用解构,也就是不声明元组,直接声明个体  
> 官方翻译为`析构`但是我们一般理解`~`函数是析构,所以这里使用了解构

```C#
(int max, int min) = Range(numbers);    //没有声明元组
Console.WriteLine(max); //直接使用max和min,无需 `.`
Console.WriteLine(min);
```

这种解构每个类都可以拥有,只需提供`Deconstruct`方法,无需任何接口.
```C#
public class Point
{
    public Point(double x, double y)
        => (X, Y) = (x, y);

    public double X { get; }
    public double Y { get; }
    //解构方法
    public void Deconstruct(out double x, out double y) =>
        (x, y) = (X, Y);
}
```
*像这样使用*
```C#
var p = new Point(3.14, 2.71);
(double X, double Y) = p;//将调用`Deconstruct`
```

编译器有时也可以帮你推断类型
```C#
int count = 5;
string label = "Colors used in the map";
var pair = (count, label); //可以推断为(int,string)
```

*如果不定义元组中元素的名字,他们默认是`Item1`,`Item2`这样的*
> 即使手动命名,默认名称任然存在.  
> `元组赋值`和`元组相等`比较不会考虑字段名称
>> 或者说他们就是使用的ItemX,而不是你给的别名.
```C#
(double, int) t1 = (4.5, 3);
Console.WriteLine($"Tuple with elements {t1.Item1} and {t1.Item2}.");
```

### 自定义元组Item名称

看一下这几个例子,就会明白,名称其实没啥用.还是Item靠谱
> 提一嘴,编译后的源代码实际上就是用的Item,你的命名都变成了`[TupleElementNamesAttribute]`的元数据
```C#
(int, double) t1 = (17, 3.14);
(double First, double Second) t2 = (0.0, 1.0);
t2 = t1;//可以使用 First,Second
Console.WriteLine($"{nameof(t2)}: {t2.First} and {t2.Second}");
// Output:
// t2: 17 and 3.14

(double A, double B) t3 = (2.0, 3.0);
t3 = t2;//可以使用A,B
Console.WriteLine($"{nameof(t3)}: {t3.A} and {t3.B}");
// Output:
// t3: 17 and 3.14
```
*元组还可以继续解构*
```C#
var t = ("post office", 3.6);
(string destination, double distance) = t;//解构
Console.WriteLine($"Distance to {destination} is {distance} kilometers.");
// Output:
// Distance to post office is 3.6 kilometers.
```

> 绕来绕去吧自己都绕蒙了  
> 建议最好是短距离使用, 这么一大坨传来传去真的很...

虽然元组看起来是为了取代`out`出现的,但实际上他的功能真的很强大.  
> `out`参数也可以是元组, 如`out(int max, int min)`  

> 有兴趣可以研究下面两个关键字`Tuple`,`ValueTuple`

## 弃元

有时候可能并不需要这么多元组数据, 如果调用返回元组的方法强制你声明这么多变量.  
可能会觉得繁琐.  

现在使用`_`关键字表示你并不需要这个元. 就叫弃元  
~~实际上是声明了一个隐式的只写变量帮你藏起来了~~  
并不是,编辑器现在提供了优化,不会实际声明这个变量.  
*但是只在同时又两个`_`在场的时候生效,如果只有一个,还是会声明一个名为`_`的变量,这是因为一个`_`是可以被程序元定义的合法名称*  
**如果上下文中有定义过如`int _ = 0;`这种就很麻烦了**

```csharp
var (_,_,_,pop1,_,pop2) = QueryCityDataForYears("New York City", 1960, 2010);
```

这样你就只拿到了你想要的两个元.

> 可以在7.0以前的版本中使用元组和弃元,只要编译器支持即可,但是效果会差一点.*不推荐吧*

## 模式匹配

扩展了is表达式, 兼顾了声明变量和as的工作

```C#
var sum = 0;
foreach(var item in values)
{
    if (item is int val)
        sum += val;
    else if (item is IEnumerable<object> subList)
        sum += DiceSum2(subList);
}
return sum;
```

次例子中演示了`item is int val`这句话表示, 如果item是一个int , 将它赋值给val, 否则`else`  
```C#
if (item is int)
{
    int val = item as int;
    sum += val;
}
```

也支持switch
```C#
public static int SumPositiveNumbers(IEnumerable<object> sequence)
{
    int sum = 0;
    foreach (var i in sequence)
    {
        switch (i)
        {
            case 0:
                break;
            case IEnumerable<int> childSequence:
            {
                foreach(var item in childSequence)
                    sum += (item > 0) ? item : 0;
                break;
            }
            case int n when n > 0://新的when语法,后面将
                sum += n;
                break;
            case null:
                throw new NullReferenceException("Null found in sequence");
            default:
                throw new InvalidOperationException("Unrecognized type");
        }
    }
    return sum;
}
```

> 注意以上代码会引起装箱拆箱.

使用`when`关键字继续判断.

```C#
switch (num)
{
    case null:
        break;
    case int n when n > 0:
        break;
    case int n when n <= 0: //同时判断两个`int`,但是又when语句做筛选条件
        break;
}
```

## 本地函数

```C#
public static IEnumerable<char> AlphabetSubset3(char start, char end)
{
    if (start < 'a' || start > 'z')
        throw new ArgumentOutOfRangeException(paramName: nameof(start), message: "start must be a letter");
    if (end < 'a' || end > 'z')
        throw new ArgumentOutOfRangeException(paramName: nameof(end), message: "end must be a letter");

    if (end <= start)
        throw new ArgumentException($"{nameof(end)} must be greater than {nameof(start)}");

    return alphabetSubsetImplementation();

    IEnumerable<char> alphabetSubsetImplementation()
    {
        for (var c = start; c < end; c++)
            yield return c;
    }
}
```
如此声明一个本地函数`alphabetSubsetImplementation`  
它只能在`AlphabetSubset3`中使用  
如此便完成了`验证参数`和`迭代器`的分离

> 这对于`async`方法很有用,可以先检查,再开始真正的异步.  
> ~~本地函数如果很简单,~~也可以使用lambda表达式

## 更多 expression-bodied 成员

```C#
public ExpressionMembersExample(string label) => this.Label = label;

~ExpressionMembersExample() => Console.Error.WriteLine("Finalized!");

private string label;

public string Label
{
    get => label;
    set => this.label = value ?? "Default label";
}
```
现在你可以为构造器, 构析器, get;set;进行简化了.  
> 但微软不推荐使用自己实现的构析器

## `ref`局部变量

你可以返回一个数据的引用类型

声明函数的时候使用`ref`关键字  
这表明返回类型为`ref`引用
```C#
public static ref int Find3(int[,] matrix, Func<int, bool> predicate)
```
返回数据的时候使用`ref`修饰  
这表明返回的数据是一个引用
```C#
return ref matrix[i, j];
```
接收函数调用的时候使用`ref`关键字定义变量  
这表明调用者知道自己在干什么
```C#
ref var item = ref MatrixSearch.Find3(matrix, (val) => val == 42);
```
如此这般, 就可以修改item达到修改matrix的效果了.~~总感觉很危险~~

## `throw`表达式

你现在可以将`throw`放置在新的地方了.  

* 条件表达式
* null合并表达式
* 一些lambda表达式

```C#
public string Name
{
    get => name;
    set => name = value ?? 
        throw new ArgumentNullException(paramName: nameof(value), message: "New name must not be null");
}
```
上面的例子演示了当value==null时候抛出异常.

## 通用的异步返回类型

你可与在`async`和`await`静态中返回其他类型了

## 数字文本语法改进

你可以声明可读性更强的数字变量了  
使用`_`来分割字符, 他们不会被当做变量使用, 只是增加可读性.

```C#
public const int Sixteen =   0b0001_0000;
public const long BillionsAndBillions = 100_000_000_000;
public const double AvogadroConstant = 6.022_140_857_747_474e23;
public const decimal GoldenRatio = 1.618_033_988_749_894_848_204_586_834_365_638_117_720_309_179M;
```

# C#7.1

## `async``Main`方法

顾名思义, 请看

```C#
public static async Task Main()
{
    
}
```

## `default`方法

以前使用`default`需要这么写

```C#
Func<string, bool> foo = default(Func<string, bool>)
```

现在可以自动推断了.
```C#
Func<string, bool> foo = default;
```

## 推断元组元素名称

可以根据右侧名称自动推断名称.

```C#
int count = 5;
string label = "Colors used in the map";
var pair = (count, label);
```
此时内容就是`count`和`label`

## 泛型类型参数的模式匹配

上面实际上通过一个 `IEnumerable<object> sequence` 演示过了.  
> 再次提醒注意装箱拆箱

# C# 7.2

## 非尾随命名方式

允许你使用`命名实参`和`正常参数`混合调用方法, 只要未命名的参数位置正确即可.

## 数值文字中的前导下划线

接上回的数字下划线, 现在16进制和2进制可以用`_`开头了

```C#
int binaryValue = 0b_0101_1100;
```

## private protected 修饰符

访问修饰符, 这指示可以可以通过同一个程序集内的派生类访问.

> 注意, 这两个修饰符组合后的关系是`且`.

## 条件ref表达式

现在可以通过简单的条件表达式来生成`ref`引用参数了.

```C#
ref var r = ref (arr != null ? ref arr[0] : ref otherArr[0]);
```

r可能是arr或otherArr的第一个参数的引用.~~很快,但是不要滥用~~

# C# 7.3

## 增强的泛型约束

现在可以使用`System.Enum`和`System.Delegate`作为约束了, 还有新增的`unmanaged`约束关键字. 指定为非托管类型, 其值和其内递归都不能是引用类型

## 元组的`==` `!=`

现在元组支持`==` `!=`比较了  
> 实际还是比Item, 要求数量,类型相同(或隐式相同)才能比较

## in 参数修饰符

```C#
static void M(S arg);
static void M(in S arg);
```
`in`关键字表示只读引用  
> 微软文档说不推荐使用~~那你为啥要做出来~~

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
