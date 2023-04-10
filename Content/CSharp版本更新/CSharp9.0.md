---
title: CSharp9.0
date: 2021-04-05 23:15:00
updated: 2021-04-05 23:15:00
id: ml-20210405-231500-g114
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---



部分内容可能需要`.Net Core`提供支持.  


> `Unity 2021.2` 支持 `C#9`  
> `Unity 2020.2` 支持 `C#8`  

- [记录类型](#记录类型)
- [仅限 Init 的资源库](#仅限-init-的资源库)
- [顶级语句](#顶级语句)
- [模式匹配增强功能](#模式匹配增强功能)
- [性能和互操作性](#性能和互操作性)
- [调整和完成功能](#调整和完成功能)
- [支持代码生成器](#支持代码生成器)
- [完毕](#完毕)

<!--more-->

# 记录类型

使用 `record` 关键字定义一个**引用类型**  
创建具有不可变属性的记录类型

```CSharp
public record Person
{
    public string FirstName { get; init; } = default!;
    public string LastName { get; init; } = default!;
};
```
`init`关键字后面讲.

也可简写为
```CSharp
public record Person(string FirstName, string LastName);
```

记录被定义为不可变, 但实际可以强制指定可变,但不推荐.
```CSharp
public record Person
{
    public string FirstName { get; set; } = default!;
    public string LastName { get; set; } = default!;
};
```

结构类型的劣势
* 不支持继承
* 确认相等性时效率较低
  * 因为`ValueType.Equals`使用反射
* 占用更多内存
  * 值拷贝

创建一条记录,就和创建一个类一样
```CSharp
Person person = new("Nancy", "Davolio");
Console.WriteLine(person);
// output: Person { FirstName = Nancy, LastName = Davolio }
```

记录会自动生成一些代码.
* 自动属性
* 构造函数
* 一个`Deconstruct`解构方法
* 一个`ToString`方法,形如`Person { FirstName = Nancy, LastName = Davolio, PhoneNumbers = System.String[] }`

> 而且使用比较操作时候, 比较值相等性,而不是引用.  
> 但是 如果一个引用类型的两个变量引用同一个对象,那么这两个变量是相等的. 如下面例子中的`phoneNumbers`

```CSharp
var phoneNumbers = new string[2];
Person person1 = new("Nancy", "Davolio", phoneNumbers);
Person person2 = new("Nancy", "Davolio", phoneNumbers);
Console.WriteLine(person1 == person2); // output: True

person1.PhoneNumbers[0] = "555-1234";
Console.WriteLine(person1 == person2); // output: True

Console.WriteLine(ReferenceEquals(person1, person2)); // output: False
```

`with` 表达式创建一个新的记录实例

```CSharp
Person person2 = person1 with { FirstName = "John" };
Console.WriteLine(person1 == person2); // output: False
person2 = person1 with { PhoneNumbers = new string[1] };
Console.WriteLine(person1 == person2); // output: False
person2 = person1 with { };
Console.WriteLine(person1 == person2); // output: True
```

一条记录可以从另一条记录继承. 但是,记录不能从类继承,类也不能从记录继承  
要使两个记录变量相等,运行时类型必须相等

```CSharp
Person teacher = new Teacher("Nancy", "Davolio", 3);
Person student = new Student("Nancy", "Davolio", 3);
Console.WriteLine(teacher == student); // output: False
```

派生类型和基类型的所有公共属性和字段都包含在 ToString 输出中

> 不可变的记录为`Hash`提供了有效支持

# 仅限 Init 的资源库

从 `C# 9.0` 开始,可为属性和索引器创建 `init` 访问器,而不是 `set` 访问器

调用方可使用属性初始化表达式语法在创建表达式中设置这些值,但构造完成后,这些属性将变为只读  
仅限 init 的资源库提供了一个窗口用来更改状态. 构造阶段结束时,该窗口关闭  
在完成所有初始化（包括属性初始化表达式和 with 表达式）之后,构造阶段实际上就结束了

```CSharp
public DateTime RecordedAt { get; init; }
//使用属性初始化表达式语法来设置值,同时仍保留不变性
var now = new WeatherObservation 
{ 
    RecordedAt = DateTime.Now, 
};
```
初始化后尝试更改观察值会导致编译器错误

对于从派生类设置基类属性,仅限 init 的资源库很有用

# 顶级语句

顶级语句从许多应用程序中删除了不必要的流程

```CSharp
using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

可以简化为
```CSharp
using System;

Console.WriteLine("Hello World!");
```

也可删除 `using` 指令,并使用完全限定的类型名称

```CSharp
System.Console.WriteLine("Hello World!");
```

应用程序中只有一个文件可使用顶级语句.  
如果编译器在多个源文件中找到顶级语句,则是错误的  
如果将顶级语句与声明的程序入口点方法（通常为 `Main` 方法）结合使用,也会出现错误  
从某种意义上讲,可认为一个文件包含通常位于 `Program` 类的 `Main` 方法中的语句

> 就是替换了Main方法,少写点代码,算是语法糖  
> 顶级语句可提供类似脚本的试验体验(代码比较少就都写在Main里面,这样就像脚本语言)

顶级语句可访问名为 `args` 的字符串数组, 并可以返回整数值  
顶级语句可能包含异步表达式. 在这种情况下,合成入口点将返回 `Task` 或 `Task<int>`

# 模式匹配增强功能

C# 9 包括新的模式匹配改进:

* 类型模式要求在变量是一种类型时匹配
* 带圆括号的模式强制或强调模式组合的优先级
* 联合 模式要求两个模式都匹配
* 析取 模式要求任一模式匹配
* 否定 模式要求模式不匹配
* 关系模式要求输入小于、大于、小于等于或大于等于给定常数.

> 最近几个版本好像都在推这个匹配模式

后面模式中的任何一种都可在允许使用模式的任何上下文中使用:`is` 模式表达式、`switch` 表达式、嵌套模式以及 `switch` 语句的 `case` 标签的模式

```CSharp
public static bool IsLetterOrSeparator(this char c) =>
    c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z') or '.' or ',';
```

# 性能和互操作性

本机大小的整数 `nint` 和 `nuint` 是整数类型. 它们由基础类型 `System.IntPtr` 和 `System.UIntPtr` 表示

函数指针提供了一种简单的语法来访问 IL 操作码 `ldftn` 和 `calli`  
可使用新的 `delegate*` 语法声明函数指针

可添加 `System.Runtime.CompilerServices.SkipLocalsInitAttribute` 来指示编译器不要发出 `localsinit` 标志,  
此标志指示 `CLR` 对所有局部变量进行零初始化  
但在某些情况下,额外的零初始化可能会对性能产生可衡量的影响

> 这些功能在某些情况下可提高性能. 仅应在采用前后对这些功能进行仔细的基准测试之后使用它们  
> 涉及本机大小整数的代码必须在使用不同整数大小的多个目标平台上进行测试. 其他功能需要不安全的代码

# 调整和完成功能

已知创建对象的类型时,可在 表达式中省略该类型
```CSharp
private List<WeatherObservation> _observations = new();//已知我要New什么了
```

当需要创建新对象作为参数传递给方法时,也可使用目标类型 new
```CSharp
public WeatherForecast ForecastFor(DateTime forecastDate, WeatherForecastOptions options)

var forecast = station.ForecastFor(DateTime.Now.AddDays(2), new());//已知参数类型,所以知道我要New什么
```

将其与仅限 `init` 的属性组合使用来初始化新对象
```CSharp
WeatherStation station = new() { Location = "Seattle, WA" };
```

可使用 `return new();` 语句返回由默认构造函数创建的实例

类似的功能可改进`条件表达式`的目标类型解析
```CSharp
int? x = condition ? 12 : null;
//var x = condition ? 12 : (int?)null; //8.0版本
```

从 `C# 9.0` 开始,可将 `static` 修饰符添加到 `匿名方法`  
`static` 修饰符可防止意外捕获其他变量(闭包问题)

协变返回类型为重写方法的返回类型提供了灵活性. 重写方法可返回从重写基方法的返回类型派生的类型. 这对于记录和其他支持虚拟克隆或工厂方法的类型很有用

此外, 循环将识别并使用扩展方法 `GetEnumerator`  
此更改意味着可以为任何类型添加 `foreach` 支持  
在设计中,应将其限制为在枚举对象有意义时使用

可使用弃元作为 `Lambda` 表达式的参数. 这样可免于为参数命名  
并且编译器也可避免使用它. 可将 `_` 用于任何参数

现在可将属性应用于`本地函数`. 例如,可将`可为空的属性注释`应用于本地函数

# 支持代码生成器

在 `C# 9.0` 之前,分部方法为 `private`,但不能指定访问修饰符、不能返回 `void`,也不能具有 `out` 参数. 这些限制意味着,如果未提供任何方法实现,编译器会删除对分部方法的所有调用  
`C# 9.0` 消除了这些限制,但要求分部方法声明必须具有实现. 代码生成器可提供这种实现  
为了避免引入中断性变更,编译器会考虑没有访问修饰符的任何分部方法,以遵循旧规则

代码生成器的第二项新功能是模块初始化表达式. 模块初始化表达式是附加了 `ModuleInitializerAttribute` 属性的方法. 在整个模块中进行任何其他字段访问或方法调用之前,运行时将调用这些方法

* 必须是静态的
* 必须没有参数
* 必须返回 void
* 不能是泛型方法
* 不能包含在泛型类中
* 必须能够从包含模块访问

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
