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

- [C#7.0](#c70)
  - [`out`变量](#out变量)
  - [元组](#元组)
  - [弃元](#弃元)
  - [模式匹配](#模式匹配)
  - [`ref`局部变量]](#ref局部变量)
  - [本地函数](#本地函数)
  - [更多 expression-bodied 成员](#更多-expression-bodied-成员)
  - [`throw`表达式](#throw表达式)
  - [通用的异步返回类型](#通用的异步返回类型)
  - [数字文本语法改进](#数字文本语法改进)
- [C#7.0](#c70-1)
  - [`async``Main`方法](#asyncmain方法)
  - [`default`方法](#default方法)
  - [推断元组元素名称](#推断元组元素名称)
- [C# 7.2](#c-72)
  - [非尾随命名方式](#非尾随命名方式)
  - [数值文字中的前导下划线](#数值文字中的前导下划线)
  - [private protected 修饰符](#private-protected-修饰符)
  - [条件ref表达式](#条件ref表达式)
- [C# 7.3](#c-73)
  - [增强的泛型约束](#增强的泛型约束)
  - [元组的`==` `!=`](#元组的-)
  - [扩展初始值设定项中的表达式变量](#扩展初始值设定项中的表达式变量)
- [完毕](#完毕)

<!--more-->

# C#7.0

## `out`变量

以前声明`out`变量的时候, 你需要先声明一个变量, 再使用`out`来修饰它.

```
int num
if(int.TryParse(input, out num))
```

现在, C#允许你在使用`out`修饰的时候直接声明变量.

```
if(int.TryParse(input, out int num))
```

别忘了, C#支持隐式变量.

```
if(int.TryParse(input, out var num))
```

这并不意味着`num`在`if`内, 其实是一个语法糖, 它与`if`是通级别的.

## 元组

元组是包含多个字段以表示数据成员的轻量级数据结构。 这些字段没有经过验证，并且你无法定义自己的方法

使用`()`创建一个元组

```
var let = ("a", "b")
```

此元组默认生成两个元素, Item1和Item2.  
你也可以显示声明元组

`(string Alpha, string Beta)namedLetters = ("a", "b")`

或者你可以从右侧指定名称

`var let = (Alpha: "a", Beta: "b")`

如果同时指定, 会得到一个警告, 右侧命名将会失效.

元组一般用于方法的返回值, 但最好是`private`或者是`internal`的, 因为那是你自己的东西.  
公开方法最好是使用定义好的`class`和`struct`  
下面演示一个方法

```
private static (int Max, int Min) Range(Ienumerable<int> num)
{
    int min = int.MinValue;
    int max = int.MaxValue;
    return (max, min)
}
```

## 弃元

有事否你可能并不需要这么多元组数据, 如果调用返回元组的方法强制你声明这么多变量.  
你可能会觉得繁琐.  

现在使用`_`关键字表示你并不需要这个元. 就叫弃元, 实际上是声明了一个隐式的只写变量帮你藏起来了.

```csharp
var (_,_,_,pop1,_,pop2) = QueryCityDataForYears("New York City", 1960, 2010);
```

这样你就只拿到了你想要的两个元.

## 模式匹配

扩展了is表达式, 兼顾了声明变量和as的工作

```
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

也支持switch

```
var sum = 0;
foreach (var item in values)
{
    switch (item)
    {
        case int val:
            sum += val;
            break;
        case IEnumerable<object> subList:
            sum += DiceSum3(subList);
            break;
    }
}
return sum;
```

使用`when`关键字继续判断.

```
switch (num)
{
    case null:
        //null logic
        break;
    case int n when n > 0:
        //positive Int logic
        break;
    case int n when n <= 0:
        //negative Int() & zero logic
        break;
}
```

## `ref`局部变量]

你可以返回一个数据的引用类型

声明函数的时候使用`ref`关键字声明返回类型为`ref`引用
```
public static ref int Find3(int[,] matrix, Func<int, bool> predicate)
```
返回数据的时候使用`ref`修饰返回的数据是一个引用
```
return ref matrix[i, j];
```
接收函数调用的时候使用`ref`关键字定义变量
```
ref var item = ref MatrixSearch.Find3(matrix, (val) => val == 42);
```
如此这般, 就可以修改item达到修改matrix的效果了.

## 本地函数
```
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

## 更多 expression-bodied 成员

```
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

## `throw`表达式

你现在可以将`throw`放置在新的地方了.  

* 条件表达式
* null合并表达式
* 一些lambda表达式

```
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

```
public const int Sixteen =   0b0001_0000;
public const long BillionsAndBillions = 100_000_000_000;
public const double AvogadroConstant = 6.022_140_857_747_474e23;
public const decimal GoldenRatio = 1.618_033_988_749_894_848_204_586_834_365_638_117_720_309_179M;
```

# C#7.0

## `async``Main`方法

顾名思义, 请看

```
public static async Task Main()
{
    
}
```

## `default`方法

以前使用`default`需要这么写

```
Func<string, bool> foo = default(Func<string, bool>)
```

现在可以自动推断了.
```
Func<string, bool> foo = default;
```

## 推断元组元素名称

可以根据右侧名称自动推断名称.

```
int count = 5;
string label = "Colors used in the map";
var pair = (count, label);
```
此时内容就是`count`和`label`

# C# 7.2

## 非尾随命名方式

允许你使用命名实参和正常参数混合调用方法, 只要未命名的参数位置正确即可.

## 数值文字中的前导下划线

接上回的数字下划线, 现在16进制和2进制可以用`_`开头了

```
int binaryValue = 0b_0101_1100;
```

## private protected 修饰符

访问修饰符, 这指示可以可以通过同一个程序集内的派生类访问.

> 注意, 这两个修饰符组合后的关系是`且`.

## 条件ref表达式

现在可以通过简单的条件表达式来生成`ref`引用参数了.

```
ref var r = ref (arr != null ? ref arr[0] : ref otherArr[0]);
```

r可能是arr或otherArr的第一个参数的引用.

# C# 7.3

## 增强的泛型约束

现在可以使用`System.Enum`和`System.Delegate`作为约束了, 还有新增的`unmanaged`约束关键字. 指定为非托管类型, 其值和其内递归都不能是引用类型

## 元组的`==` `!=`

现在元组支持`==` `!=`比较了

## 扩展初始值设定项中的表达式变量

现在支持在类初始化传参时候指定自动`out`变量

```
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


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
