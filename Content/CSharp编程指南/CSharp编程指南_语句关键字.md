---
title: CSharp编程指南_语句关键字
date: 2021-04-20 23:17:00
updated: 2021-04-20 23:17:00
id: ml-20210420-231700-g124
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

语句是程序指令。除了本文提到的关键字内容，语句都是按照顺序执行

| 类别         | C# 关键字                                        |
| ------------ | ------------------------------------------------ |
| 选择语句     | if、else、switch、case                           |
| 迭代语句     | do、for、foreach、in、while                      |
| 跳转语句     | break、continue、default、goto、return、yield    |
| 异常处理语句 | throw、try-catch、try-finally、try-catch-finally |
| Checked 和   | unchecked	checked、unchecked                     |
| fixed 语句   | fixed                                            |
| lock 语句    | lock                                             |


<!--more-->

# 选择语句

## if-else

`if` 语句基于布尔表达式的值来识别运行哪个语句
```C#
bool condition = true;
if (condition)
{
    Console.WriteLine("The variable is set to true.");
}
else
{
    Console.WriteLine("The variable is set to false.");
}
```
*当然,`else`并非是必要的,语句可以只有`if`*

**可以将任何有效的布尔表达式用于条件(`condition`)**

## switch-case

`switch` 是一个选择语句，它根据与匹配表达式匹配的模式，从候选列表中选择单个开关部分进行执行  
当选项较多时,`switch` 语句通常用作 `if-else` 构造的替代项
```C#
int caseSwitch = 1;
switch (caseSwitch)
{
	case 1:
		Console.WriteLine("Case 1");
		break;
	case 2:
		Console.WriteLine("Case 2");
		break;
	default:
		Console.WriteLine("Default case");
		break;
}
```
*可以看到,每个`case`都必须由`break`来跳出,`C#`不允许非指定的贯穿,避免了错误*  

需要贯穿
* 在`case`语句没有内容,而是后紧跟其他case语句
* 使用`goto`关键字

```C#
switch (caseSwitch)
{
    case 1:
        Console.WriteLine("Case 1...");
        break;
    case 2:
    case 3:
        Console.WriteLine("... and/or Case 2");
        break;
    case 4:
        while (true)
           Console.WriteLine("Endless looping. . . .");
	case 5:
		 goto case 1;
    default:
        Console.WriteLine("Default value...");
        break;
}
```
*可见,`while(true)`也可以保证不贯穿,不过并不建议*

*从 `C# 7.0` 开始，匹配表达式(`caseSwitch`)可以是任何非 `null` 表达式*  
*在 `C# 7.0` 中，因为支持其他模式，所以 `case` 标签不需要定义互斥值，并且多个模式可以与匹配表达式相匹配。 因为仅执行包含匹配模式的首次开关部分中的语句，所以 `case` 语句显示的顺序很重要*

如果匹配表达式与其他任何 `case` 标签都不匹配，`default case` 指定要执行的 `switch` 部分。 如果没有 `default case`，且匹配表达式与其他任何 `case` 标签都不匹配，程序流就会贯穿 `switch` 语句

注意下面这种泛型用法

```C#
private static void ShowCollectionInformation<T>(T coll)
{
	switch (coll)
	{
		case Array arr:
			Console.WriteLine($"An array with {arr.Length} elements.");
			break;
		case IEnumerable<int> ieInt:
			Console.WriteLine($"Average: {ieInt.Average(s => s)}");
			break;
		case IList list:
			Console.WriteLine($"{list.Count} items");
			break;
		case IEnumerable ie:
			string result = "";
			foreach (var e in ie)
				result += $"{e} ";
			Console.WriteLine(result);
			break;
		case object o:
			Console.WriteLine($"A instance of type {o.GetType().Name}");
			break;
		default:
			Console.WriteLine("Null passed to this method.");
			break;
	}
}
```

# 迭代语句

## do

指定的布尔表达式的计算结果为 `true` 时，`do` 语句会执行  
在进行这次判断之前,`do`最少执行一次. 所以表达式写在尾部

```C#
int n = 0;
do
{
    Console.WriteLine(n);
    n++;
} while (n < 5);
```

## for

在指定的布尔表达式的计算结果为 `true` 时，`for` 语句会执行

```C#
for (int i = 0; i < 5; i++)
{
    Console.WriteLine(i);
}
```
*在任何没有`true`的时候,`for`终止*

### 初始化表达式

指`int i = 0`部分  

`初始化表达式`部分的语句仅在进入循环前执行一次 

### 条件部分

指`i < 5`部分  

`条件`部分（如果存在）必须为布尔表达式 。 在每次循环迭代前计算该表达式。 如果“条件”部分不存在或者布尔表达式的计算结果为 `true`，则执行下一个循环迭代；否则退出循环

*所以可以不存在,那就是等同于`true`,也就是死循环*
```C#
for (int i = 0; ; i++)
{
	Console.WriteLine(i);
}
```

### 迭代器部分

指`i++`部分  

`迭代器`部分定义循环主体的每次迭代后将执行的操作 。 `迭代器`部分包含用逗号分隔的零个或多个以下语句表达式

以下示例定义无限 `for` 循环, 但是不推荐这样使用,建议使用显示的`true`

```C#
for ( ; ; )
{
    // Body of the loop.
}
```

## foreach,in

`foreach` 语句为类型实例中实现 `System.Collections.IEnumerable` 或 `System.Collections.Generic.IEnumerable<T>` 接口的每个元素执行

```C#
var fibNumbers = new List<int> { 0, 1, 1, 2, 3, 5, 8, 13 };
int count = 0;
foreach (int element in fibNumbers)
{
    Console.WriteLine($"Element #{count}: {element}");
    count++;
}
Console.WriteLine($"Number of elements: {count}");
```

`foreach` 语句并不限于这些类型。 可以将其与满足以下条件的任何类型的实例一起使用
* 类型具有公共无参数 `GetEnumerator` 方法，其返回类型为类、结构或接口类型。 
  * 从 C# 9.0 开始，`GetEnumerator` 方法可以是类型的扩展方法。
* `GetEnumerator` 方法的返回类型具有公共 `Current` 属性和公共无参数 `MoveNext` 方法

*从 `C# 7.3` 开始，如果枚举器的 `Current` 属性返回引用返回值（`ref T`，其中 `T` 为集合元素类型），就可以使用 `ref` 或 `ref readonly` 修饰符来声明迭代变量*

*从 `C# 8.0` 开始，可以使用 `await foreach` 语句来使用异步数据流，即实现 `IAsyncEnumerable<T>` 接口的集合类型。 异步检索下一个元素时，可能会挂起循环的每次迭代。 下面的示例演示如何使用 `await foreach` 语句*
```C#
await foreach (var item in GenerateSequenceAsync())
{
    Console.WriteLine(item);
}
```
*默认情况下，在捕获的上下文中处理流元素。 如果要禁用上下文捕获，请使用 `TaskAsyncEnumerableExtensions.ConfigureAwait` 扩展方法*

如果 `foreach` 语句的源集合为空，则 `foreach` 循环的正文不会被执行，而是被跳过

可以使用 `var` 关键字让编译器推断 `foreach` 语句中迭代变量的类型.

# while

在指定的布尔表达式的计算结果为 `true` 时，`while` 语句会执行
```C#
int n = 0;
while (n < 5)
{
    Console.WriteLine(n);
    n++;
}
```
*与`do`语句相似,但是第一次就要判断表达式.*

# 跳转语句

## break

`break` 语句将终止其所在位置的最接近封闭循环或 `switch` 语句。 控制权将传递给已终止语句后面的语句（若有）

打断`for`
```C#
for (int i = 1; i <= 100; i++)
{
	if (i == 5)
	{
		break;
	}
	Console.WriteLine(i);
}
```
*同理可应用至任何迭代语句*

打断`case`.
```C#
switch (n)
{
	case 1:
		Console.WriteLine("Current value is 1");
		break;
	case 2:
		Console.WriteLine("Current value is 2");
		break;
	case 3:
		Console.WriteLine("Current value is 3");
		break;
	default:
		Console.WriteLine("Sorry, invalid selection.");
		break;
}
```
*C#要求每个case都要有`break`,避免无意识的贯穿*

## continue

`continue` 语句将控制传递到其中出现的封闭 `while`、`do`、`for` 或 `foreach` 语句的下一次迭代

```C#
for (int i = 1; i <= 10; i++)
{
	if (i < 9)
	{
		continue;
	}
	Console.WriteLine(i);
}
```
*也就是说,可以用作任何迭代语句*

## goto

`goto` 语句将程序控制直接传递给标记语句

```C#
switch (n)
{
	case 1:
		cost += 25;
		break;
	case 2:
		cost += 25;
		goto case 1;
	case 3:
		cost += 50;
		goto case 1;
	default:
		Console.WriteLine("Invalid selection.");
		break;
}
```

也可以自定义标签,随意跳转

```C#
for (int i = 0; i < x; i++)
{
	for (int j = 0; j < y; j++)
	{
		if (array[i, j].Equals(myNumber))
		{
			goto Found;
		}
	}
}

Console.WriteLine($"The number {myNumber} was not found.");
goto Finish;

Found://自定义标签Found
Console.WriteLine($"The number {myNumber} is found.");

Finish://自定义标签Finish
Console.WriteLine("End of search.");
```

## return

`return` 语句可终止它所在的方法的执行，并将控制权返回给调用方法。 它还可以返回可选值。 如果方法是 `void` 类型，则 `return` 语句可以省略

```C#
static double CalculateArea(int r)
{
	double area = r * r * Math.PI;
	return area;
}
```

# 异常处理语句

## throw

发出程序执行期间出现异常的信号

```C#
throw new IndexOutOfRangeException();
```
*可抛出任何派生自 `System.Exception` 的类的实例*

*从 `C# 7.0` 开始，`throw` 可以用作表达式和语句。 这允许在以前不支持的上下文中引发异常*

### 条件运算符

```C#
 string arg = args.Length >= 1 ? args[0] : throw new ArgumentException("You must supply an argument");
```

### null 合并运算符

```C#
name = value ?? throw new ArgumentNullException(paramName: nameof(value), message: "Name cannot be null");
```

### expression-bodied lambda 或方法

```C#
DateTime ToDateTime(IFormatProvider provider) => throw new InvalidCastException("Conversion to a DateTime is not supported.");
```

## try-catch-finally

### try

运行在`try`中的语句引发的异常将被捕获

```C#
object o2 = null;
try
{
    int i2 = (int)o2;   // Error
}
```
*此时引发`NullReferenceException`异常*  
但是此异常需要由`catch`语句捕获

### catch

由`try`捕获的异常,将交由`catch`处理.
```C#
object o2 = null;
try
{
    int i2 = (int)o2;   // Error
}
catch (NullReferenceException e)
{
	//...
}
catch (InvalidCastException e)
{
	//...
}
catch (Exception e)
{
	//...
}
```
可以使用同一 `try-catch` 语句中的多个特定 `catch` 子句。 在这种情况下，`catch` 子句的顺序很重要，因为 `catch` 子句是按顺序检查的

尽管可以不带参数使用 `catch` 子句来捕获任何类型的异常，但不推荐这种用法。 一般情况下，只应捕获你知道如何从其恢复的异常。

也可以使用异常筛选器进一步检查该异常以决定是否要对其进行处理。 如果异常筛选器返回 `false`，则继续搜索处理程序

```C#
catch (ArgumentException e) when (e.ParamName == "…")
{
}
```

异常筛选器要优于捕获和重新引发，因为筛选器将保留堆栈不受损坏

你可以捕获一个异常而引发一个不同的异常。 执行此操作时，请指定作为内部异常捕获的异常，如以下示例所示

```C#
catch (InvalidCastException e)
{
    // Perform some action here, and then throw a new exception.
    throw new YourCustomException("Put your error message here.", e);
}
```

对于异步的异常处理

```C#
public async Task DoMultipleAsync()
{
    Task theTask1 = ExcAsync(info: "First Task");
    Task theTask2 = ExcAsync(info: "Second Task");
    Task theTask3 = ExcAsync(info: "Third Task");

    Task allTasks = Task.WhenAll(theTask1, theTask2, theTask3);

    try
    {
        await allTasks;
    }
    catch (Exception ex)
    {
        Debug.WriteLine("Exception: " + ex.Message);
        Debug.WriteLine("Task IsFaulted: " + allTasks.IsFaulted);
        foreach (var inEx in allTasks.Exception.InnerExceptions)
        {
            Debug.WriteLine("Task Inner Exception: " + inEx.Message);
        }
    }
}

private async Task ExcAsync(string info)
{
    await Task.Delay(100);

    throw new Exception("Error-" + info);
}
```

### finally

通过使用 `finally` 块，可以清除 `try` 块中分配的任何资源，即使在 `try` 块中发生异常，也可以运行代码

通常情况下，`finally` 块的语句会在控件离开 `try` 语句时运行。 正常执行中，执行 `break`、`continue`、`goto` 或 `return` 语句，或者从 `try` 语句外传播异常都可能会导致发生控件转换

```C#
finally
{
	// To run the program in Visual Studio, type CTRL+F5. Then
	// click Cancel in the error dialog.
	Console.WriteLine("\nExecution of the finally block after an unhandled\n" +
		"error depends on how the exception unwind operation is triggered.");
	Console.WriteLine("i = {0}", i);
}
```
*可以跟随在`try`或`catch`之后*

**合并使用如下**

```C#
string path = @"c:\users\public\test.txt";
System.IO.StreamReader file = new System.IO.StreamReader(path);
char[] buffer = new char[10];
try
{
	file.ReadBlock(buffer, index, buffer.Length);
}
catch (System.IO.IOException e)
{
	Console.WriteLine("Error reading from {0}. Message = {1}", path, e.Message);
}

finally
{
	if (file != null)
	{
		file.Close();
	}
}
```

# checked和unchecked语句

C# 语句既可以在已检查的上下文中执行，也可以在未检查的上下文中执行  
在已检查的上下文中，算法溢出引发异常  
在未选中的上下文中忽略算术溢出并将结果截断，方法是：丢弃任何不适应目标类型的高序位

* `checked` 指定已检查的上下文。
* `unchecked` 指定未检查的上下文。

下列操作受溢出检查的影响
* 表达式在整型上使用下列预定义运算符
  * `++`，`--`，`一元`,`-`，`+`，`-`，`*`，`/`
* 整型类型之间或从 `float` 或 `double` 到整型类型的显式数字转换

*如果既未指定 `checked`，也未指定 `unchecked`*  
将有编译器选项的值定义

## checked

默认情况下，如果表达式仅包含常量值，且产生的值在目标类型范围之外，则会导致编译器错误  
如果表达式包含一个或多个非常量值，则编译器不检测溢出

```C#
int ten = 10;
int i2 = 2147483647 + ten;//不会报错
Console.WriteLine(i2);//-2,147,483,639 //溢出了
```

使用关键字来强制检查

```C#
checked
{
    int i3 = 2147483647 + ten;
    Console.WriteLine(i3);//OverflowException error
}
```

## unchecked

同理,就是强制不检查

```C#
unchecked
{
    int1 = 2147483647 + 10;//不会报错
}
```

# fixed语句

`fixed` 语句可防止垃圾回收器重新定位可移动的变量。 `fixed` 语句仅允许存在于不安全的上下文中

略.

# lock语句

`lock` 语句获取给定对象的互斥 `lock`，执行语句块，然后释放 `lock`

持有 `lock` 时，持有 `lock` 的线程可以再次获取并释放 `lock`.  
且阻止任何其他线程获取 `lock` 并等待释放 `lock`

```C#
lock (x)
{
    // Your code...
}
```

其中 `x` 是引用类型的表达式, 它完全等同于

```C#
object __lockObj = x;
bool __lockWasTaken = false;
try
{
    System.Threading.Monitor.Enter(__lockObj, ref __lockWasTaken);
    // Your code...
}
finally
{
    if (__lockWasTaken) System.Threading.Monitor.Exit(__lockObj);
}
```
*由于该代码使用 `try...finally` 块，即使在 `lock` 语句的正文中引发异常，也会释放 `lock`。*

在 `lock` 语句的正文中不能使用 `await` 运算符

当同步对共享资源的线程访问时，请锁定专用对象实例

避免对不同的共享资源使用相同的 `lock` 对象实例，因为这可能导致死锁或锁争用

具体而言，避免将以下对象用作 `lock` 对象
* `this`（调用方可能将其用作 `lock`）。
* `Type` 实例（可以通过 `typeof` 运算符或反射获取）。
* 字符串实例，包括字符串文本，（这些可能是暂存的）。

尽可能缩短持有锁的时间，以减少锁争用

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
