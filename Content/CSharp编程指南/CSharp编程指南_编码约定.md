---
title: CSharp编程指南_基本概念
date: 2021-04-13 22:23:00
updated: 2021-04-13 22:23:00
id: ml-20210413-222300-g119
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

# 编码约定

来自微软官方文档和C#本质论.  
具体规定还需以实际项目为准.

<!--more-->

# 常用规范

常用规范来自

# 命名规范

命名规范来自`.Net`文档.  
框架设计准则->命名准则

## 大小写预定

`PascalCasing` 约定用于除了参数名称外的所有标识符，使每个单词的第一个字符大写.

`CamelCasing` 约定仅用于参数名称，将每个单词的第一个字符（除第一个单词之外）设为大写

> 实际上`CamelCasing`还用做非公开字段.

**不要假设所有编程语言都区分大小写。 它们不是。 名称不能单独区分**

## 通用命名约定

* 选择易于阅读的标识符名称
* 提高可读性比简洁更好
* **不要**使用下划线、连字符或任何其他非字母数字字符
  * 吐槽下:下划线也不能用吗?用了好多年了...
* **不要**使用匈牙利表示法
* 避免使用与广泛使用的编程语言的关键字冲突的标识符
* 避免使用常见关键字的`@`版本
* **不要**使用缩写作为标识符名称的一部分
* 要使用未被广泛接受的任何首字母缩写，即使被广泛接受,也只在必要时使用
* 确实使用有语义的名称，而不是类型名称的特定于语言的关键字
  * 例如， `GetLength` 是比`GetInt` 更好的名称 
  * 在极少数情况下,如果标识符没有超出其类型的语义含义,则使用其语言的特定名称, 如`ToInt64`而不是`ToLong`(因为 `Int64` 是特定于 `c #` 的别名)
* 使用公用名（如 `value` 或 `item` ），而不是重复类型名称
* 命名现有 Api 的新版本时
  * 使用类似于旧 API 的名称
  * 添加后缀（而非前缀）
    * 这将有助于在浏览文档,旧版本的 API 将被组织到新的 Api 附近,因为大部分文本阅读器都有基于首字母排序
  * 考虑使用全新但有意义的标识符,而不是重复标识符
  * 使用数字后缀来指示现有 API 的新版本
    * 除非 API 的现有名称是有意义的唯一名称, 例如行业标准
  * **不要**使用标识符的 "Ex" (或类似的) 后缀来区分它与早期版本的同一 API

## 程序集和 DLL 的名称

`<Company>.<Component>.dll`

## 命名空间的名称

* 使用公司名称作为命名空间名称的前缀，以防不同公司的命名空间具有相同的名称
* 在命名空间名称的第二级使用稳定的独立于版本的产品名称
* **不要**使用组织层次结构作为命名空间层次结构中的名称的基础，因为公司内的组名通常是短期的
* 使用 `PascalCasing`
* 考虑在适当的情况下使用复数命名空间名称
  * 例如，请使用 `System.Collections `而不是` System.Collection`
  * 品牌名称和首字母缩写是此规则的例外情况
  * 使用 `System.IO` 而不是 `System.IOs`
* **不要**为命名空间和该命名空间中的类型使用相同的名称
* **不要**引入泛型类型名称如`Element`,`Node`,`Message `
* 不要提供会与核心命名空间中的任何类型冲突的类型名称
  * 这里的`核型`一般指`System`或你项目中经常,必须使用的空间.

> 以上内容只是建议,还是应当以公司内部规定为主

## 类、结构和接口的名称

* **不要**为类名称指定前缀
* 考虑用基类的名称结束派生类的名称
  * 一种 `ArgumentOutOfRangeException`, `Exception`
  * 一种 `SerializableAttribute`, `Attribute` 
* 使用字母 `I` 来为接口名称加上前缀，以指示该类型是接口
* 使用描述性名称命名泛型类型参数
* 考虑使用 `T` 作为具有一个单字母类型参数的类型的类型参数名称

当命名类型派生自或实现某些 .NET Framework 类型时， 按照下表中所述的指导原则进行操作

| 基类型                           | 实现类型准则                                                                                                 |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| System.Attribute                 | ✔️将后缀 "Attribute" 添加到自定义特性类的名称中                                                               |
| System.Delegate                  | ✔️将后缀 "EventHandler" 添加到在事件中使用的委托的名称                                                        |
|                                  | ✔️将后缀 "Callback" 添加到作为事件处理程序使用的委托的名称                                                    |
|                                  | ❌ 不要将后缀 "Delegate" 添加到委托。                                                                         |
| System.EventArgs                 | ✔️添加后缀 "EventArgs"                                                                                        |
| System.Enum                      | ❌ 不要从此类派生;改为使用您的语言支持的关键字;例如，在 c # 中，使用 enum 关键字                              |
|                                  | ❌ 不要添加后缀 "Enum" 或 "标志"                                                                              |
| System.Exception                 | ✔️添加后缀 "Exception"                                                                                        |
| IDictionary                      |                                                                                                              |
| IDictionary<TKey,TValue>         | ✔️添加后缀 "Dictionary"。 请注意， IDictionary 是一种特定类型的集合，但是此准则优先于下面的更常见的集合原则。 |
| IEnumerable                      |                                                                                                              |
| ICollection                      |                                                                                                              |
| IList                            |                                                                                                              |
| IEnumerable<T>                   |                                                                                                              |
| ICollection<T>                   |                                                                                                              |
| IList<T>                         | ✔️添加后缀 "Collection"。                                                                                     |
| System.IO.Stream                 | ✔️添加后缀 "Stream"。                                                                                         |
| CodeAccessPermission IPermission | ✔️添加后缀 "Permission"                                                                                       |

> **不要**在枚举类型名称中使用 "Enum" 后缀  
> **不要**在枚举类型名称中使用 "Flag" 或 "Flags" 后缀。
> **不要**在枚举值名称上使用前缀 (例如，将 "ad" 用于 ADO 枚举，使用 "rtf" 进行丰富文本枚举等 ) 

## 类型成员的名称

* **不要**有与 "Get" 方法的名称相匹配的属性
* 在布尔属性前面添加 "Is"、"Can" 或 "has" 前缀
* 考虑为属性提供与其类型相同的名称
* 例如，以下属性可正确获取和设置名为 `Color` 的枚举值，因此属性名为 `Color`

```C#
public enum Color {...}
public class Control {
    public Color Color { get {...} set {...} }
}
```

* 不要使用 "Before" 或 "After" 前后缀来表示事件的先后.
* 命名事件处理程序应该使用 "EventHandler" 后缀的事件类型
* 用 "EventArgs" 后缀命名事件参数类

* 不要对字段名称使用前缀

## 命名参数

* 考虑使用基于参数含义而不是参数类型的名称
* **不要**对运算符重载参数名称使用缩写或数值索引

## 命名资源

* 在命名资源中仅使用字母数字字符和下划线
* 对异常消息资源使用以下命名约定
  * 资源标识符应为异常类型名称和异常的简短标识符
  * ArgumentExceptionIllegalCharacters 
  * ArgumentExceptionInvalidName 
  * ArgumentExceptionFileNameIsMalformed

## 额外

标识符不应包含两个连续的 `_` 字符。 这些名称保留给编译器生成的标识符

# 布局约定

* 使用默认的代码编辑器设置(智能缩进、4 字符缩进、制表符保存为空格)
* 每行只写一条语句
* 每行只写一个声明
* 如果连续行未自动缩进,请将它们缩进一个制表符位(四个空格).
* 在方法定义与属性定义之间添加至少一个空白行
* 使用括号突出表达式中的子句，如下面的代码所示

```C#
if ((val1 > val2) && (val1 > val3))
{
    // Take appropriate action.
}
```

# 注释约定

* 将注释放在单独的行上，而非代码行的末尾
* 以大写字母开始注释文本
* 以句点结束注释文本
* 在注释分隔符 `//` 与注释文本之间插入一个空格,如下面的示例所示
* 请勿在注释周围创建格式化的星号块,
  * 即不要混用`//`和`/**/`

```C#
// The following declaration creates a query. It does not run
// the query.
```

# 语言准则

## 字符串

*使用字符串内插来编写文本字面量*

```C#
string displayName = $"{nameList[n].LastName}, {nameList[n].FirstName}";
```

*经常追加字符串,请使用`StringBuilder`*

```C#
var phrase = "lalalalalalalalalalalalalalalalalalalalalalalalalalalalalala";
var manyPhrases = new StringBuilder();
for (var i = 0; i < 10000; i++)
{
    manyPhrases.Append(phrase);
}
//Console.WriteLine("tra" + manyPhrases);
```

## 隐式类型本地变量

* 当变量类型明显来自赋值的右侧时,或者当精度类型不重要时,请对本地变量进行隐式类型化
* 当类型并非明显来自赋值的右侧时,请勿使用 `var`
  * 请勿假设类型明显来自方法名称, 即使对于`X.ToInt()`函数,也不应该使用`var`
  * 为`new`运算符, 推荐用`var`
  * 显式强制转换, 推荐`var`
* 请勿使用名称来代指类型, 如`var inputInt`, 这是不正确的.
* 需要运行时进行推理,请使用`dynamic`
* 对于`for`循环,使用`var`来定义`i`.
* 但对于`foreach`循环,则不应该使用`var`来定义`item`

> 对于所有迭代,不应该修改其内部的值, 运行时会报错.

## 无符号数据类型

通常使用`int`, 这更容易与其他开发者交互.

## 数组

如果能确认初始化内容,则使用简易的初始化

*简易初始化不能使用`var`*
```C#
string[] vowels1 = { "a", "e", "i", "o", "u" };
```

*显示初始化可以使用`var`*
```C#
var vowels2 = new string[] { "a", "e", "i", "o", "u" };
```

## 委托

尽量使用`C#`内置的`Func<>`和`Action<>`, 这有利于其他开发者理解其内容.

*简单方法可以直接使用委托形式创建*
```C#
public static Action<string> ActionExample1 = x => Console.WriteLine($"x is: {x}");

public static Action<string, string> ActionExample2 = (x, y) => 
    Console.WriteLine($"x is: {x}, y is {y}");

public static Func<string, int> FuncExample1 = x => Convert.ToInt32(x);

public static Func<int, int, int> FuncExample2 = (x, y) => x + y;
```

## 异常处理

对于可能发生异常的地方,要积极的使用`try-catch`语句

通过使用`using`语句来调用`IDisposable`释放资源.

> 在 `C# 8` 及更高版本中，使用无需大括号的新的 `using` 语法

```C#
//C#8.0
using Font font3 = new Font("Arial", 10.0f);
byte charset3 = font3.GdiCharSet;
```

## &&和||运算符

尽量使用短路运算符`&&`和`||`  
它们尽可能少的计算表达式, 对以下列子很有用

```C#
if (pizz != null && pizz.IsOver())
{
	 Console.WriteLine($"pizz = {pizz}");
}
```

如果此时使用的是非短路的`&`,则必定会执行后面的一个表达式,导致有可能抛出空引用异常.  
而短路形式的`&&`则会终止与`pizz != null`, 不再执行后续内容.

## new运算符

使用`var`来调用`new`关键字

```C#
var instance1 = new ExampleClass();
```

使用对象初始化器
```C#
var instance3 = new ExampleClass 
	{ 
		Name = "Desktop",
		ID = 37414,
		Location = "Redmond",
		Age = 2.3 
	};
```

> 如果可能,多使用`default`关键字来初始化变量

## 事件处理

对于临时的函数,使用`lambda`表达式

## 静态成员

总是使用最明确的路径调用静态成员`ClassName.StaticMember`  
如果使用子类调用父类的静态成员,则子类自己可以使用相同成员,并破坏代码的预期.

## LINQ查询

对于查询结果的集合总是用有意义的名字,避免大家总是看繁琐的表达式.  
对于查询结果的集合总是声明`var`变量存储

对于查询出来的小写字段,使用别名包装成大写,用于遵守`Pascal `风格

```C#
var localDistributors =
    from customer in customers
    join distributor in distributors on customer.City equals distributor.City
    select new { Customer = customer, Distributor = distributor };
```

> 如果有必要,为这些结果重命名为更准确的词汇,比如用`CustomerName`代替`Name`

对齐 `from` 子句下的查询子句.

在其他查询子句之前使用 `where` 子句，以确保后面的查询子句作用于经过减少和筛选的数据集

使用多行 from 子句代替 join 子句以访问内部集合

# 

# 安全性

略.

请查看官方安全编码准则.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
