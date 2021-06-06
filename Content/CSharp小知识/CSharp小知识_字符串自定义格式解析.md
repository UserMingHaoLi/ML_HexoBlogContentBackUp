---
title: CSharp小知识-字符串自定义格式解析
date: 2021-05-29 21:38:00
updated: 2021-05-29 21:38:00
id: ml-20210529-213800-g142
categories:
	- CSharp小知识
tags: 
	- CSharp小知识
	- CSharp
---

就是`ICustomFormatter`,`IFormatProvider`,`IFormattable`三兄弟

<!--more-->

```C#
static void Main(string[] args)
{
	MyFormattable pFormattable = new MyFormattable();
	string printString;
	MyFormatProvider myFormater = new MyFormatProvider();
	printString = string.Format(myFormater, "{0}", pFormattable);
	Console.WriteLine("{0}", printString);
	printString = string.Format(myFormater, "{0:C}", pFormattable);
	Console.WriteLine("{0}", printString);
	printString = string.Format(myFormater, "{0:MyFormater}", pFormattable);
	Console.WriteLine("{0}", printString);

	Console.Read();
}
}
public class MyFormatProvider : IFormatProvider
{
public object GetFormat(Type format)
{
	if (format == typeof(ICustomFormatter))
		return new MyCustomFormatter();
	return null;
}
}
public class MyCustomFormatter : ICustomFormatter
{
public string Format(string format, object arg, IFormatProvider provider)
{
	if (format == null)
	{
		if (arg is IFormattable)
			return ((IFormattable)arg).ToString(format, provider);
		return arg.ToString();
	}
	else
	{
		if (format == "MyFormater")
		{
			return "CustomFormatter Case MyFormater + " + arg.ToString();
		}
		else
		{
			if (arg is IFormattable)
				return ((IFormattable)arg).ToString(format, provider);
			return arg.ToString();
		}
	}
}
}
public class MyFormattable : IFormattable
{
public string ToString(string format, IFormatProvider formatProvider)
{
	if(format == "C")
	{
		return "Formattable Case C";
	}
	return "Formattable Case Default";
}

public override string ToString()
{
	return "Override Object ToString";
}
}
```

格式设置是指将类、结构或枚举值的实例转换为其字符串表示形式的过程，通常使得最终的字符串可以显示给用户，或者进行反序列化以还原为原始数据类型。 此转换可能面临一系列挑战

* 在内部存储值的方式不一定反映用户想要查看它们的方式。 例如，电话号码可以存储为 `8009999999` 格式，但此格式并非是用户友好的格式。 该电话号码应显示为 `800-999-9999`
* 有时对象到其字符串表示形式的转换不是直观的。 例如，不清楚 `Temperature` 对象或 `Person` 对象的字符串表示形式应如何显示
* 值通常需要区分区域性的格式。 例如，在使用数字表示货币值的应用程序中，数字字符串应包括当前区域性的货币符号、组分隔符（在大多数区域性中，组分隔符为千位分隔符）和小数点符号
* 应用程序可能需要以不同方式显示相同的值。 例如，应用程序可能通过显示名称的字符串表示形式来表示一个枚举成员，或通过显示基础值来表示该枚举成员

===

格式设置的基本机制是 `Object.ToString` 方法的默认实现,  
不过，.NET 提供了几种方法来修改和扩展其默认格式设置支持

* 重写 Object.ToString 方法以定义对象值的自定义字符串表示形式
* 定义格式说明符，格式说明符允许对象值的字符串表示形式采用多种形式。 例如，以下语句中的“X”格式说明符将整数转换为十六进制值的字符串表示形式
```C#
int integerValue = 60312;
Console.WriteLine(integerValue.ToString("X"));   // Displays EB98.
```
* 使用格式提供程序以利用特定区域性的格式设置约定。 例如，以下语句通过使用 en-US 区域性的格式设置约定来显示货币值。
```C#
double cost = 1632.54;
Console.WriteLine(cost.ToString("C",
                  new System.Globalization.CultureInfo("en-US")));
// The example displays the following output:
//       $1,632.54
```
* 实现 `IFormattable` 接口可以支持使用 `Convert` 类的字符串转换以及复合格式设置
* 使用复合格式设置来嵌入较大字符串中值的字符串表示形式
* 实现 `ICustomFormatter` 和 `IFormatProvider` 可以提供完全自定义的格式设置解决方案

===

# 使用 `ToString` 方法的默认格式设置

每个从 `System.Object` 派生的类型都自动继承无参数的 `ToString` 方法，该方法在默认情况下返回类型的名称

> 自 `Windows 8.1` 起，`Windows` 运行时包括具有单个方法 `IStringable.ToString` 的 `IStringable` 接口  
> 建议托管类型不实现 `IStringable` 接口

若要提供可提供该对象相关信息的对象的字符串表示形式，必须重写 `ToString` 方法  

结构继承自 `ValueType`，而后者又派生自 `Object`, `ValueType`一般都有重写`ToString()`

```C#
using System;

public class Temperature
{
   private decimal temp;

   public Temperature(decimal temperature)
   {
      this.temp = temperature;
   }

   public override string ToString()
   {
      return this.temp.ToString("N1") + "°C";
   }
}

public class Example
{
   public static void Main()
   {
      Temperature currentTemperature = new Temperature(23.6m);
      Console.WriteLine("The current temperature is " +
                        currentTemperature.ToString());
   }
}
// The example displays the following output:
//       The current temperature is 23.6°C.
```
*此例子重写了`Temperature`类的`ToString`方法,以便打印温度*

> 大多数重写方法调用 `ToString` 方法的另一个重载并向其传递"G"(一般格式)

# 格式字符

对象具有单一字符串表示形式时，可以依赖于默认 `ToString` 方法或重写 `ToString` 。 但是，对象的值通常具有多种表示形式。 例如，温度可以用*华氏度*、*摄氏度*或*开氏度*来表示。 同样，整数值 *10* 可以表示为多种形式，包括 *10、10.0、1.0e01 或 $10.00*

为了允许单个值具有多种字符串表示形式，.NET 使用格式字符串  
格式字符串是包含一个或多个预定义格式说明符的字符串，这些格式说明符是单一字符或字符组，用于定义 `ToString` 方法应如何设置其输出格式  
.NET 中的所有数字类型、日期和时间类型以及枚举类型都支持一组预定义的格式说明符。 还可以使用格式字符串定义你应用程序所定义的数据类型的多种字符串表示形式

 ## 标准格式字符串

`标准格式字符串`包含单个`格式说明符`  
该格式说明符是一个字母字符，用于定义应用该格式说明符的对象的字符串表示形式，此外，它还包含一个可选的`精度说明符`，该精度说明符影响在结果字符串中显示的位数  
如果省略或不支持精度说明符，则标准格式说明符等效于标准格式字符串

传递给枚举值的 `ToString` 方法的格式字符串决定是使用其字符串名称（“G”和“F”格式说明符）、基础整数值（“D”格式说明符）还是十六进制值（“X”格式说明符）来显示值  
```C#
DayOfWeek thisDay = DayOfWeek.Monday;
string[] formatStrings = {"G", "F", "D", "X"};

foreach (string formatString in formatStrings)
   Console.WriteLine(thisDay.ToString(formatString));
// The example displays the following output:
//       Monday
//       Monday
//       1
//       00000001
```

数字类型的标准格式字符串通常定义一个结果字符串，该结果字符串的确切显示由一个或多个属性值控制。 例如，“C”格式说明符会将数字的格式设置为货币值

此外，数字格式字符串可以包含一个精度说明符。 该说明符的含义取决于与其一起使用的格式字符串，但是，它通常指示应在结果字符串中显示的总位数或小数位数

```C#
byte[] byteValues = { 12, 163, 255 };
foreach (byte byteValue in byteValues)
   Console.WriteLine(byteValue.ToString("X4"));
// The example displays the following output:
//       000C
//       00A3
//       00FF
```

日期和时间值的标准格式字符串是由特定 `DateTimeFormatInfo` 属性存储的自定义格式字符串的别名

```C#
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      DateTime date1 = new DateTime(2009, 6, 30);
      Console.WriteLine("D Format Specifier:     {0:D}", date1);
      string longPattern = CultureInfo.CurrentCulture.DateTimeFormat.LongDatePattern;
      Console.WriteLine("'{0}' custom format string:     {1}",
                        longPattern, date1.ToString(longPattern));
   }
}
// The example displays the following output when run on a system whose
// current culture is en-US:
//    D Format Specifier:     Tuesday, June 30, 2009
//    'dddd, MMMM dd, yyyy' custom format string:     Tuesday, June 30, 2009
```

还可以使用标准格式字符串来定义应用程序所定义的对象的字符串表示形式  
应支持下列各项
* 个“G”格式说明符，表示对象的常用或通用格式
* 支持等于空引用的格式说明符

```C#
public string ToString(string format)
{
	// Handle null or empty string.
	if (String.IsNullOrEmpty(format)) format = "C";
	// Remove spaces and convert to uppercase.
	format = format.Trim().ToUpperInvariant();

	// Convert temperature to Fahrenheit and return string.
	switch (format)
	{
		// Convert temperature to Fahrenheit and return string.
		case "F":
		return this.Fahrenheit.ToString("N2") + " °F";
		// Convert temperature to Kelvin and return string.
		case "K":
		return this.Kelvin.ToString("N2") + " K";
		// return temperature in Celsius.
		case "G":
		case "C":
		return this.Celsius.ToString("N2") + " °C";
		default:
		throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
	}
}
```

# 自定义格式字符串




# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
