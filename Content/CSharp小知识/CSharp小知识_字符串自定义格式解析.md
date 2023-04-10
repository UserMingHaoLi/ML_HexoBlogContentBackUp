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

```CSharp
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

格式设置是指将类、结构或枚举值的实例转换为其字符串表示形式的过程,通常使得最终的字符串可以显示给用户,或者进行反序列化以还原为原始数据类型。 此转换可能面临一系列挑战

* 在内部存储值的方式不一定反映用户想要查看它们的方式。 例如,电话号码可以存储为 `8009999999` 格式,但此格式并非是用户友好的格式。 该电话号码应显示为 `800-999-9999`
* 有时对象到其字符串表示形式的转换不是直观的。 例如,不清楚 `Temperature` 对象或 `Person` 对象的字符串表示形式应如何显示
* 值通常需要区分区域性的格式。 例如,在使用数字表示货币值的应用程序中,数字字符串应包括当前区域性的货币符号、组分隔符（在大多数区域性中,组分隔符为千位分隔符）和小数点符号
* 应用程序可能需要以不同方式显示相同的值。 例如,应用程序可能通过显示名称的字符串表示形式来表示一个枚举成员,或通过显示基础值来表示该枚举成员

===

格式设置的基本机制是 `Object.ToString` 方法的默认实现,  
不过,.NET 提供了几种方法来修改和扩展其默认格式设置支持

* 重写 Object.ToString 方法以定义对象值的自定义字符串表示形式
* 定义格式说明符,格式说明符允许对象值的字符串表示形式采用多种形式。 例如,以下语句中的"X"格式说明符将整数转换为十六进制值的字符串表示形式
```CSharp
int integerValue = 60312;
Console.WriteLine(integerValue.ToString("X"));   // Displays EB98.
```
* 使用格式提供程序以利用特定区域性的格式设置约定。 例如,以下语句通过使用 en-US 区域性的格式设置约定来显示货币值。
```CSharp
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

每个从 `System.Object` 派生的类型都自动继承无参数的 `ToString` 方法,该方法在默认情况下返回类型的名称

> 自 `Windows 8.1` 起,`Windows` 运行时包括具有单个方法 `IStringable.ToString` 的 `IStringable` 接口  
> 建议托管类型不实现 `IStringable` 接口

若要提供可提供该对象相关信息的对象的字符串表示形式,必须重写 `ToString` 方法  

结构继承自 `ValueType`,而后者又派生自 `Object`, `ValueType`一般都有重写`ToString()`

```CSharp
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

对象具有单一字符串表示形式时,可以依赖于默认 `ToString` 方法或重写 `ToString` 。 但是,对象的值通常具有多种表示形式。 例如,温度可以用*华氏度*、*摄氏度*或*开氏度*来表示。 同样,整数值 *10* 可以表示为多种形式,包括 *10、10.0、1.0e01 或 $10.00*

为了允许单个值具有多种字符串表示形式,.NET 使用格式字符串  
格式字符串是包含一个或多个预定义格式说明符的字符串,这些格式说明符是单一字符或字符组,用于定义 `ToString` 方法应如何设置其输出格式  
.NET 中的所有数字类型、日期和时间类型以及枚举类型都支持一组预定义的格式说明符。 还可以使用格式字符串定义你应用程序所定义的数据类型的多种字符串表示形式

 ## 标准格式字符串

`标准格式字符串`包含单个`格式说明符`  
该格式说明符是一个字母字符,用于定义应用该格式说明符的对象的字符串表示形式,此外,它还包含一个可选的`精度说明符`,该精度说明符影响在结果字符串中显示的位数  
如果省略或不支持精度说明符,则标准格式说明符等效于标准格式字符串

传递给枚举值的 `ToString` 方法的格式字符串决定是使用其字符串名称（"G"和"F"格式说明符）、基础整数值（"D"格式说明符）还是十六进制值（"X"格式说明符）来显示值  
```CSharp
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

数字类型的标准格式字符串通常定义一个结果字符串,该结果字符串的确切显示由一个或多个属性值控制。 例如,"C"格式说明符会将数字的格式设置为货币值

此外,数字格式字符串可以包含一个精度说明符。 该说明符的含义取决于与其一起使用的格式字符串,但是,它通常指示应在结果字符串中显示的总位数或小数位数

```CSharp
byte[] byteValues = { 12, 163, 255 };
foreach (byte byteValue in byteValues)
   Console.WriteLine(byteValue.ToString("X4"));
// The example displays the following output:
//       000C
//       00A3
//       00FF
```

日期和时间值的标准格式字符串是由特定 `DateTimeFormatInfo` 属性存储的自定义格式字符串的别名

```CSharp
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
* 个"G"格式说明符,表示对象的常用或通用格式
* 支持等于空引用的格式说明符

```CSharp
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

如果格式字符串仅包含一个自定义格式说明符,则此格式说明符前面应带有百分比 (%) 符号,以免与标准格式说明符混淆
```CSharp
DateTime date1 = new DateTime(2009, 9, 8);
Console.WriteLine(date1.ToString("%M"));       // Displays 9
```
日期和时间值的许多标准格式字符串均是由 `DateTimeFormatInfo` 对象的属性所定义的自定义格式字符串的别名

```CSharp
string customFormat = "MMMM dd, yyyy (dddd)";
DateTime date1 = new DateTime(2009, 8, 28);
Console.WriteLine(date1.ToString(customFormat));
// The example displays the following output if run on a system
// whose language is English:
//       August 28, 2009 (Friday)
```
```CSharp
using System;

public class Example
{
   public static void Main()
   {
      long number = 8009999999;
      string fmt = "000-000-0000";
      Console.WriteLine(number.ToString(fmt));
   }
}
// The example displays the following output:
//        800-999-9999
```

在 .NET 中,此附加格式设置信息通过 `IFormatProvider` 接口提供

`IFormatProvider` 接口包含一个 `GetFormat(Type)`方法,该方法只有一个参数,该参数指定提供格式设置信息的对象类型。 如果该方法可以提供该类型的对象,则返回它。 否则,它返回空引用

这对应开篇代码块中的`MyFormatProvider`类,其中的`if`可以扩充为更多选择

`GetFormat` 方法负责将提供所需格式设置信息的对象返回给 `ToString` 方法,也就是说实际上是一个由`ToString`调用的回调方法.

> 如果方法没有 `IFormatProvider` 类型的参数,则改为传递 `CultureInfo.CurrentCulture` 属性所返回的对象。 例如,对默认 `Int32.ToString()` 方法的调用最终将导致诸如以下的方法调用： `Int32.ToString("G", System.Globalization.CultureInfo.CurrentCulture)`

.NET 提供了三个实现 `IFormatProvider` 的类

* `DateTimeFormatInfo` 类,提供特定区域性的日期和时间值的格式设置信息
* `NumberFormatInfo` 类,提供特定区域性的数字格式设置信息
* `CultureInfo` 。 其 `IFormatProvider.GetFormat` 实现可以返回一个 `NumberFormatInfo` 对象（可提供数字格式设置信息）或一个 `DateTimeFormatInfo` 对象（可提供日期和时间值的格式设置信息）
 
# 数值的区分区域性的格式设置

默认情况下,数值的格式设置是区分区域性的。 如果在调用格式设置方法时不指定区域性,则将使用当前线程区域性的格式设置约定

```CSharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      Decimal value = 1043.17m;

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}",
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(value.ToString("C2"));
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//       The current culture is en-US
//       $1,043.17
//
//       The current culture is fr-FR
//       1 043,17 €
//
//       The current culture is es-MX
//       $1,043.17
//
//       The current culture is de-DE
//       1.043,17 €
```

# 日期和时间值的区分区域性的格式设置

默认情况下,日期和时间值的格式设置是区分区域性的。 如果在调用格式设置方法时不指定区域性,则将使用当前线程区域性的格式设置约定

```CSharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      DateTime dateToFormat = new DateTime(2012, 5, 28, 11, 30, 0);

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}",
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(dateToFormat.ToString("F"));
         Console.WriteLine();
      }
   }
}
// The example displays the following output:
//       The current culture is en-US
//       Monday, May 28, 2012 11:30:00 AM
//
//       The current culture is fr-FR
//       lundi 28 mai 2012 11:30:00
//
//       The current culture is es-MX
//       lunes, 28 de mayo de 2012 11:30:00 a.m.
//
//       The current culture is de-DE
//       Montag, 28. Mai 2012 11:30:00
```

# IFormattable 接口

通常 `IFormatProvider` 方法的类型还实现 `IFormattable` 接口

此接口具有一个成员 `IFormattable.ToString(String, IFormatProvider)`,该成员同时将格式字符串和格式提供程序作为参数

对应用程序定义的类实现 `IFormattable` 接口具有两大优势

* 支持使用 `Convert` 类进行字符串转换。 对 `Convert.ToString(Object)` 和 `Convert.ToString(Object, IFormatProvider)` 方法的调用会自动调用 `IFormattable` 实现

* 支持复合格式设置。 如果使用包含格式字符串的格式项设置自定义类型的格式,则公共语言运行时自动调用 IFormattable 实现,并向其传递该格式字符串

```CSharp
public string ToString(string format, IFormatProvider provider)
{
	// Handle null or empty arguments.
	if (String.IsNullOrEmpty(format))
		format = "G";
	// Remove any white space and covert to uppercase.
	format = format.Trim().ToUpperInvariant();

	if (provider == null)
		provider = NumberFormatInfo.CurrentInfo;

	switch (format)
	{
		// Convert temperature to Fahrenheit and return string.
		case "F":
		return this.Fahrenheit.ToString("N2", provider) + "°F";
		// Convert temperature to Kelvin and return string.
		case "K":
		return this.Kelvin.ToString("N2", provider) + "K";
		// Return temperature in Celsius.
		case "C":
		case "G":
		return this.Celsius.ToString("N2", provider) + "°C";
		default:
		throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
	}
}

public static void Main()
{
	CultureInfo.CurrentCulture = CultureInfo.GetCultureInfo("en-US");
	Temperature temp = new Temperature(22m);
	Console.WriteLine(Convert.ToString(temp, new CultureInfo("ja-JP")));
	Console.WriteLine("Temperature: {0:K}", temp);
	Console.WriteLine("Temperature: {0:F}", temp);
	Console.WriteLine(String.Format(new CultureInfo("fr-FR"), "Temperature: {0:F}", temp));
}
```

# 复合格式设置

一些方法（如 `String.Format` 和 `StringBuilder.AppendFormat`）支持复合格式设置。 复合格式字符串是一种模板,该模板返回合并了零个、一个或多个对象的字符串表示形式的单一字符串

```CSharp
String.Format("On {0:d}, the inventory of {1} was worth {2:C2}.",
                       thatDate, item1, item1.Value);
```

> 如果版本允许,官方推荐使用`字符串内插`

所以,可使用格式化字符串的方式由`%X`和`{0:X}`这两种格式

> 还可以使用`,`来限制宽度和对齐

```CSharp
DateTime startDate = new DateTime(2015, 8, 28, 6, 0, 0);
decimal[] temps = { 73.452m, 68.98m, 72.6m, 69.24563m,
                   74.1m, 72.156m, 72.228m };
Console.WriteLine("{0,-20} {1,11}\n", "Date", "Temperature");
for (int ctr = 0; ctr < temps.Length; ctr++)
   Console.WriteLine("{0,-20:g} {1,11:N1}", startDate.AddDays(ctr), temps[ctr]);

// The example displays the following output:
//       Date                 Temperature
//
//       8/28/2015 6:00 AM           73.5
//       8/29/2015 6:00 AM           69.0
//       8/30/2015 6:00 AM           72.6
//       8/31/2015 6:00 AM           69.2
//       9/1/2015 6:00 AM            74.1
//       9/2/2015 6:00 AM            72.2
//       9/3/2015 6:00 AM            72.2
```

使用 `ICustomFormatter` 进行自定义格式设置

两种复合格式设置方法`（ String.Format(IFormatProvider, String, Object[])` 和 `StringBuilder.AppendFormat(IFormatProvider, String, Object[])）`都包括一个支持自定义格式设置的格式提供程序

当调用其中一种格式设置方法时,该方法会将表示 `Type` 接口的 `ICustomFormatter` 对象传递到格式提供程序的 `GetFormat` 方法

表现为`GetFormat(typeof(ICustomFormatter))`

然后 `GetFormat` 方法负责返回提供自定义格式设置功能的 `ICustomFormatter` 实现

`ICustomFormatter` 接口具有一个方法 `Format(String, Object, IFormatProvider)`,复合格式设置方法为复合格式字符串中的每一格式项自动调用一次该方法.  
也就是每个`%X`或每个`{0:X}`

`Format(String, Object, IFormatProvider)` 方法具有三个参数：一个格式字符串、一个要设置格式的对象和一个提供格式设置服务的 `IFormatProvider` 对象  
通常,实现 `ICustomFormatter` 的类还会实现 `IFormatProvider,因此上述最后一个参数是对自定义格式设置类自身的引用`  
该方法返回要设置格式的对象的带格式自定义字符串表示形式。 如果该方法无法设置对象的格式,则应返回空引用

对于开篇的代码段来说,三个参数分别如下
```CSharp
printString = string.Format(myFormater, "{0}", pFormattable);
//null, pFormattable, myFormater
printString = string.Format(myFormater, "{0:C}", pFormattable);
//C, pFormattable, myFormater
printString = string.Format(myFormater, "{0:MyFormater}", pFormattable);
//MyFormater, pFormattable, myFormater
```
*也就是格式字符, 被转化者, 转化提供者*

# 关键字

格式字符串,用于匹配到转化函数,通常为`%G`,`{0:G}`, 匹配成功会后会有两个参数,一个要转化的对象,也就是`Object`,另一个就是下面的格式说明符,指示如何转化

格式说明符,指`Format(string, object, IFormatProvider)`中的`String`参数, 用于代指怎么转化,如`G`通常转化

# 流程

整套转化流程都依赖外界的回调调用,最先被调用的是`myFormater`,用于通过`type`参数筛选出一个符合外界需求的格式化器(`ICustomFormatter`).  
之后格式化器被调用,并将筛选器也透传到里面,方便进一步透传,此时的`object arg`就是被`{0}`所解析到的数据源. 依据解析器的能力, 决定如何解析,或者使用该类型的默认解析,或者返回null,都由解析器自己决定,反正最后`return string`来生成结果即可

> 解析器可能调用另外的其他解析器,形成套娃.


# 标准数字格式字符串

标准数字格式字符串用于格式化通用数值类型。 标准数字格式字符串采用 `Axx` 的形式,其中

* `A` 是称为"格式说明符"的单个字母字符。 任何包含一个以上字母字符（包括空白）的数字格式字符串都被解释为自定义数字格式字符串
* `xx` 是称为"精度说明符"的可选整数。 精度说明符的范围从 0 到 99,并且影响结果中的位数
  *  请注意,精度说明符控制数字的字符串表示形式中的数字个数。 它不舍入该数字。 若要执行舍入运算,请使用 `Math.Ceiling`、`Math.Floor` 或 `Math.Round` 方法

> 若要使用前导或尾随空格填充结果字符串,请使用 *复合格式*设置功能,并在格式项中定义 *对齐组件*

下列支持标准数字格式字符串

* 所有数字类型的一些 `ToString` 方法重载。 例如 `Int32.ToString(String)` 和 `Int32.ToString(String, IFormatProvider)`
* .NET 复合格式功能,由 `Console` 和 `StreamWriter` 类的一些 `Write` 和 `WriteLine` 方法、`String.Format` 方法以及 `StringBuilder.AppendFormat` 方法使用
* `内插字符串`,与*复合格式字符串*相比,语法更简化

* "C"或"c"	货币
  * 123.456 ("C", en-US) -> $123.46
  * 123.456 ("C", fr-FR) -> 123,46 €
* "D"或"d"	十进制
  * 1234 ("D") -> 1234
  * -1234 ("D6") -> -001234
* "E"或"e"	指数（科学型）
* "F"或"f"	定点
* "G"或"g"	常规
* "N"或"n"	数字
* "P"或"p"	百分比
* "R"或"r"	往返过程
* "X"或"x"	十六进制
* 任何其他单个字符	未知说明符	结果:在运行时引发 `FormatException。`

```CSharp
decimal value = 123.456m;
Console.WriteLine(value.ToString("C2"));
// Displays $123.46
```
*此例子中,默认区域为`en-US`*

```CSharp
decimal[] amounts = { 16305.32m, 18794.16m };
Console.WriteLine("   Beginning Balance           Ending Balance");
Console.WriteLine("   {0,-28:C2}{1,14:C2}", amounts[0], amounts[1]);
// Displays:
//        Beginning Balance           Ending Balance
//        $16,305.32                      $18,794.16
```
*此例子中,在 28 位字符的字段中左对齐货币值,在 14 位字符的字段中右对齐货币值,并都是用两位小数点*

## 自定义数字格式字符串

### "0"自定义说明符

"0"自定义格式说明符用作零占位符符号。 如果要设置格式的值在格式字符串中出现零的位置有一个数字,则将此数字复制到结果字符串中；否则,在结果字符串中显示零

小数点前最左边的零的位置和小数点后最右边的零的位置确定总在结果字符串中出现的数字范围

"00"说明符使得值被舍入到小数点前最近的数字,其中零位总被舍去。 例如,用"00"格式化 34.5 将得到值 35

```CSharp
value = 123;
Console.WriteLine(value.ToString("00000"));
Console.WriteLine(String.Format("{0:00000}", value));
// Displays 00123
```

### "#"自定义说明符

"#"符号的位置有一个数字,则此数字被复制到结果字符串中。 否则,结果字符串中的此位置不存储任何值

请注意,如果零不是有效数字,此说明符永不显示零,即使零是字符串中的唯一数字也是如此。 仅当零是所显示的数字中的有效数字时,才会显示零

"##"格式字符串使得值被舍入到小数点前最近的数字,其中零总被舍去。 例如,用"##"格式化 34.5 将得到值 35

```CSharp
value = 1.2;
Console.WriteLine(value.ToString("#.##", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:#.##}", value));
// Displays 1.2
value = 123456;
Console.WriteLine(value.ToString("[##-##-##]"));
Console.WriteLine(String.Format("{0:[##-##-##]}", value));
 // Displays [12-34-56]
```

若要返回空缺数字或前导零替换为空格的结果字符串,请使用 *复合格式功能* 并指定字段宽度,如以下示例所示
```CSharp
Double value = .324;
Console.WriteLine("The value is: '{0,5:#.###}'", value);
//The value is: ' .324'
```
*包含了`索引组件`0, `对齐组件`5, `格式字符串组件` #.##*

### "."自定义说明符

插入本地化的小数分隔符  
格式字符串中的第一个小数点确定设置了格式的值中的小数分隔符的位置；任何其他小数点会被忽略

在结果字符串中用作小数分隔符的字符并非总是小数点；它由控制格式设置的 `NumberDecimalSeparator` 对象的 `NumberFormatInfo` 属性确定

```CSharp
value = 1.2;
Console.WriteLine(value.ToString("0.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:0.00}", value));
// Displays 1.20

Console.WriteLine(value.ToString("00.00", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:00.00}", value));
// Displays 01.20
```

### ","自定义说明符

组分隔符和数字比例换算说明符

如果在两个设置数字的整数位格式的数字占位符（0 或 #）之间指定一个或多个逗号,则在输出的整数部分中的每个数字组之间插入一个组分隔符字符

当前 `NumberGroupSeparator` 对象的 `NumberGroupSizes` 和 `NumberFormatInfo` 属性将确定用作数字组分隔符的字符以及每个数字组的大小  
例如,如果使用字符串"#,#"和固定区域性对数字 1000 进行格式化,则输出为"1,000"

如果在紧邻显式或隐式小数点的左侧指定一个或多个逗号,则对于每个逗号,将要设置格式的数字除以 1000

如果使用字符串"0,,"对数字 100000000 进行格式化,则输出为"100"

```CSharp
double value = 1234567890;
Console.WriteLine(value.ToString("#,#", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:#,#}", value));
// Displays 1,234,567,890

Console.WriteLine(value.ToString("#,##0,,", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:#,##0,,}", value));
// Displays 1,235
```
### "%"自定义说明符

将使数字在设置格式之前乘以 100

本地化的百分比符号插入到数字在格式字符串中出现 % 的位置。 使用的百分比字符由当前 `PercentSymbol` 对象的 `NumberFormatInfo` 属性定义

```CSharp
double value = .086;
Console.WriteLine(value.ToString("#0.##%", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:#0.##%}", value));
// Displays 8.6%
```

### "‰"自定义说明符

基本同上
```CSharp
double value = .00354;
string perMilleFmt = "#0.## " + '\u2030';
Console.WriteLine(value.ToString(perMilleFmt, CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:" + perMilleFmt + "}", value));
// Displays 3.54‰
```

### "E"和"e"自定义说明符

如果"E"、"E+"、"E-"、"e"、"e+"或"e-"中的任何一个字符串出现在格式字符串中,而且后面紧跟至少一个零,则数字用科学记数法来设置格式

跟在科学记数法指示符后面的零的数目确定指数输出的最小位数

```CSharp
double value = 86000;
Console.WriteLine(value.ToString("0.###E+0", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:0.###E+0}", value));
// Displays 8.6E+4

Console.WriteLine(value.ToString("0.###E+000", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:0.###E+000}", value));
// Displays 8.6E+004

Console.WriteLine(value.ToString("0.###E-000", CultureInfo.InvariantCulture));
Console.WriteLine(String.Format(CultureInfo.InvariantCulture,
                                "{0:0.###E-000}", value));
// Displays 8.6E004
```

### "\"转义字符

若要防止某个字符被解释为格式说明符,你可以在该字符前面加上反斜杠（即转义字符）

若在要结果字符串中包括反斜杠,必须使用另一个反斜杠 (`\\`)

```CSharp
Console.WriteLine(value.ToString(@"\#\#\# ##0 dollars and \0\0 cents \#\#\#"));
Console.WriteLine(String.Format(@"{0:\#\#\# ##0 dollars and \0\0 cents \#\#\#}",
                                value));
// Displays ### 123 dollars and 00 cents ###

Console.WriteLine(value.ToString("\\\\\\\\\\\\ ##0 dollars and \\0\\0 cents \\\\\\\\\\\\"));
Console.WriteLine(String.Format("{0:\\\\\\\\\\\\ ##0 dollars and \\0\\0 cents \\\\\\\\\\\\}",
                                value));
// Displays \\\ 123 dollars and 00 cents \\\
```

### ";"部分分隔符

对数字应用不同的格式设置,具体取决于值为正、为负还是为零  
类似于`if-else`的控制机制

第一部分应用于正值,第二部分应用于负值,第三部分应用于零。

第二部分可以留空（分号间没有任何内容）,在这种情况下,第一部分应用于所有非零值。

如果要设置格式的数字为非零值,但根据第一部分或第二部分中的格式舍入后为零,则最终的零根据第三部分进行格式设置。

```CSharp
double posValue = 1234;
double negValue = -1234;
double zeroValue = 0;

string fmt2 = "##;(##)";
string fmt3 = "##;(##);**Zero**";

Console.WriteLine(posValue.ToString(fmt2));
Console.WriteLine(String.Format("{0:" + fmt2 + "}", posValue));
// Displays 1234

Console.WriteLine(negValue.ToString(fmt2));
Console.WriteLine(String.Format("{0:" + fmt2 + "}", negValue));
// Displays (1234)

Console.WriteLine(zeroValue.ToString(fmt3));
Console.WriteLine(String.Format("{0:" + fmt3 + "}", zeroValue));
// Displays **Zero**
```

### 字符文本

所有其他字符始终解释为字符文本,在格式设置操作中,将按原样包含在结果字符串中

```CSharp
double n = 123.8;
Console.WriteLine($"{n:#,##0.0K}");
// The example displays the following output:
//      123.8K
```
*K就是文本直接包含在字符串中*

如果想使用特殊字符为文本形式,则需要

* 通过对格式字符进行转义处理`\`
* 通过将整个文本字符串括在单引号中

```CSharp
double n = 9.3;
Console.WriteLine($@"{n:##.0\%}");
Console.WriteLine($@"{n:\'##\'}");
Console.WriteLine($@"{n:\\##\\}");
Console.WriteLine();
Console.WriteLine($"{n:##.0'%'}");
Console.WriteLine($@"{n:'\'##'\'}");
// The example displays the following output:
//      9.3%
//      '9'
//      \9\
//
//      9.3%
//      \9\
```

## 说明

```CSharp
double number1 = 1234567890;
string value1 = number1.ToString("(###) ###-####");
Console.WriteLine(value1);

int number2 = 42;
string value2 = number2.ToString("My Number = #");
Console.WriteLine(value2);
// The example displays the following output:
//       (123) 456-7890
//       My Number = 42
```

### 浮点型无穷大和 NaN

格式字符串就分别是当前适用的 PositiveInfinitySymbol对象指定的 NegativeInfinitySymbol、 NaNSymbol 或 NumberFormatInfo 属性的值。

### 控制面板设置

控制面板中 "区域和语言选项" 项中的设置会影响由格式化操作产生的结果字符串  
因为这是不指定区域的默认区域

### 舍入和定点格式字符串

对于固定点格式字符串（即不包含科学记数法格式字符的格式字符串）,数字被舍入为与小数点右边的数字占位符数目相同的小数位数  
如果格式字符串不包含小数点,数字被舍入为最接近的整数  
如果数字位数多于小数点左边数字占位符的个数,多余的数字被复制到结果字符串中紧挨着第一个数字占位符的前面

### 使用特定数目的前导零填充整数

```CSharp
int value = 160934;
int decimalLength = value.ToString("D").Length + 5;
int hexLength = value.ToString("X").Length + 5;
Console.WriteLine(value.ToString("D" + decimalLength.ToString()));
Console.WriteLine(value.ToString("X" + hexLength.ToString()));
// The example displays the following output:
//       00000160934
//       00000274A6
```

> 标准格式字符串只是自定义格式字符串的别名  
> 即`G`可以表示`###`也可以表示`##.##`,取决于具体实现  
> **这很重要,因为日期和时间值的字符串表示形式通常会因区域性而异**

例如,"d"标准格式字符串指示应使用短日期模式显示日期和时间值。 对于固定区域性,此模式为"MM/dd/yyyy"。 对于 fr-FR 区域性,此模式为"dd/MM/yyyy"。 对于 ja-JP 区域性,此模式为"yyyy/MM/dd"

# 标准日期和时间格式字符串

定义 `DateTime` 或 `DateTimeOffset` 值的文本表示形式

* "d"	短日期模式。
  * 2009-06-15T13:45:30 -> 6/15/2009 (en-US)
* "D"	长日期模式
  * 2009-06-15T13:45:30 -> Monday, June 15, 2009 (en-US)
* "f"	完整日期/时间模式（短时间）
  * 2009-06-15T13:45:30 -> Monday, June 15, 2009 1:45 PM (en-US)
* "F"	完整日期/时间模式（长时间）。
  * 2009-06-15T13:45:30 -> Monday, June 15, 2009 1:45:30 PM (zh-CN)
* "g"	常规日期/时间模式（短时间）。
  * 2009-06-15T13:45:30 -> 6/15/2009 1:45 PM (en-US)
* "G"	常规日期/时间模式（长时间）。
  * 2009-06-15T13:45:30 -> 6/15/2009 1:45:30 PM (en-US)
* "M"、"m"	月/日模式。
  * 2009-06-15T13:45:30 -> June 15 (en-US)
* "O"、"o"	往返日期/时间模式。
  * 2009-06-15T13:45:30 (DateTimeKind.Local) --> 2009-06-15T13:45:30.0000000-07:00
* "R"、"r"	RFC1123 模式。
  * 2009-06-15T13:45:30 -> Mon, 15 Jun 2009 20:45:30 GMT
* "s"	可排序日期/时间模式。
  * 2009-06-15T13:45:30 (DateTimeKind.Local) -> 2009-06-15T13:45:30
* "t"	短时间模式。
  * 2009-06-15T13:45:30 -> 1:45 PM (en-US)
* "T"	长时间模式。
  * 2009-06-15T13:45:30 -> 1:45:30 PM (en-US)
* "u"	通用可排序日期/时间模式。
  * 带有 DateTime 值：2009-06-15T13:45:30 -> 2009-06-15 13:45:30Z
  * 带有 DateTimeOffset 值：2009-06-15T13:45:30 -> 2009-06-15 20:45:30Z
* "U"	通用完整日期/时间模式
  * 2009-06-15T13:45:30 -> Monday, June 15, 2009 8:45:30 PM (en-US)
* "Y"、"y"	年月模式。
  * 2009-06-15T13:45:30 -> June 2009 (en-US)

*注意这里区分大小写g和G*

"O"（或"o"）、"R"（或"r"）、"s"和"u"。 这些字符串表示形式在各个区域性中都应是相同的

* "O"或"o"		
  * yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffzz
* "R"或"r"		
  * ddd, dd MMM yyyy HH':'mm':'ss 'GMT'
* "s"		
  * *yyyy'-'MM'-'dd'T'HH':'mm':'ss
* "u"		
  * yyyy'-'MM'-'dd HH':'mm':'ss'Z'

通过调用 `DateTimeFormatInfo.GetAllDateTimePatterns(Char)` 方法,你可以确定与标准格式字符串对应的自定义格式字符串

```CSharp
Console.WriteLine("'d' standard format string:");
foreach (var customString in DateTimeFormatInfo.CurrentInfo.GetAllDateTimePatterns('d'))
	Console.WriteLine("   {0}", customString);
// The example displays the following output:
//       'd' standard format string:
//          M/d/yyyy
//          M/d/yy
//          MM/dd/yy
//          MM/dd/yyyy
//          yy/MM/dd
//          yyyy-MM-dd
//          dd-MMM-yy
```


## 自定义日期和时间格式字符串

```CSharp
DateTime thisDate1 = new DateTime(2011, 6, 10);
Console.WriteLine("Today is " + thisDate1.ToString("MMMM dd, yyyy") + ".");

DateTimeOffset thisDate2 = new DateTimeOffset(2011, 6, 10, 15, 24, 16,
                                              TimeSpan.Zero);
Console.WriteLine("The current date and time: {0:MM/dd/yy H:mm:ss zzz}",
                   thisDate2);
// The example displays the following output:
//    Today is June 10, 2011.
//    The current date and time: 06/10/11 15:24:16 +00:00
```
*前文说到,格式化字符串实际就是对于自定义格式字符串的一种包装.*

下表描述自定义日期和时间格式说明符并显示由每个格式说明符生成的结果字符串

* "d"	一个月中的某一天（1 到 31）。
* "dd"	一个月中的某一天（01 到 31）。
* "ddd"	一周中某天的缩写名称。
* "dddd"	一周中某天的完整名称。
* "f"	日期和时间值的十分之几秒。
* "ff"	日期和时间值的百分之几秒。
* "fff"	日期和时间值的千分之几秒。
* "ffff"	日期和时间值的万分之几秒。
* "fffff"	日期和时间值的十万分之几秒。
* "ffffff"	日期和时间值的百万分之几秒。
* "fffffff"	日期和时间值的千万分之几秒。
* "F"	如果非零,则为日期和时间值的十分之几秒
* "FF"	如果非零,则为日期和时间值的百分之几秒
* "FFF"	如果非零,则为日期和时间值的千分之几秒
* "FFFF"	如果非零,则为日期和时间值的万分之几秒
* "FFFFF"	如果非零,则为日期和时间值的十万分之几秒
* "FFFFFF"	如果非零,则为日期和时间值的百万分之几秒
* "FFFFFFF"	如果非零,则为日期和时间值的千万分之几秒
* "g"、"gg"	时期或纪元。
* "h"	采用 12 小时制的小时（从 1 到 12）
* "hh"	采用 12 小时制的小时（从 01 到 12）
* "H"	采用 24 小时制的小时（从 0 到 23）。
* "HH"	采用 24 小时制的小时（从 00 到 23）
* "K"	时区信息。
* "m"	分钟（0 到 59）。
* "mm"	分钟（00 到 59）
* "M"	月份（1 到 12）。
* "MM"	月份（1 到 12）。
* "MMM"	月份的缩写名称。
* "MMMM"	月份的完整名称。
* "s"	秒（0 到 59）。
* "ss"	秒（00 到 59）。
* "t"	AM/PM 指示符的第一个字符。
* "tt"	AM/PM 指示符。
* "y"	年份（0 到 99）。
* "yy"	年份（00 到 99）。
* "yyyy"	由四位数字表示的年份。
* "yyyyy"	由五位数字表示的年份。
* "z"	相对于 UTC 的小时偏移量,无前导零。
* "zz"	相对于 UTC 的小时偏移量,带有表示一位数值的前导零。
* "zzz"	相对于 UTC 的小时和分钟偏移量。
* ":"	时间分隔符。
* "/"	日期分隔符。
* "string" 文本字符串分隔符。
  * 2009-06-15T13:45:30 ("arr:" h:m t) -> arr:1:45 P 
  * 也就是自定义
* %	将下面的字符定义为自定义格式说明符
* \	转义字符
* 任何其他字符	字符将复制到未更改的结果字符串
  * 2009-06-15T01:45:30 (arr hh:mm t) -> arr 01:45 A

# 标准 TimeSpan 格式字符串

```CSharp
TimeSpan duration = new TimeSpan(1, 12, 23, 62);
string output = "Time of Travel: " + duration.ToString("c");
Console.WriteLine(output);
Console.WriteLine("Time of Travel: {0:c}", duration);
// The example displays the following output:
//       Time of Travel: 1.12:24:02
//       Time of Travel: 1.12:24:02
```

* "c"	常量（固定）格式
  * 此说明符不区分区域性。 它的形式是 [-][d'.']hh':'mm':'ss['.'fffffff]。（"t"格式与"T"格式字符串产生的结果相同。）
  * New TimeSpan(3, 17, 25, 30, 500) -> 3.17:25:30.5000000
* "g"	常规短格式
  * 该说明符仅输出需要的内容。 它区分区域性并采用 [-][d':']h':'mm':'ss[.FFFFFFF] 形式。
  * New TimeSpan(1, 3, 16, 50, 599) -> 1:3:16:50.599 (en-US)
* "G"	常规长格式
  * 此说明符始终输出天数和七个小数位。 它区分区域性并采用 [-]d':'hh':'mm':'ss.fffffff 形式。
  * New TimeSpan(18, 30, 0) -> 0:18:30:00.0000000 (en-US)

方括号 ([ and ]) 中的元素是可选的。 句点 (.) 和冒号 (:) 是文字符号。 下表介绍了剩余的元素

| 元素    | 描述                                                                                                                                        |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| -       | 可选负号,指示负时间间隔。                                                                                                                  |
| d       | 不带前导零的可选天数。                                                                                                                      |
| hh      | 小时数,范围为"00"到"23"。                                                                                                                  |
| mm      | 分钟数,范围为"00"到"59"。                                                                                                                  |
| ss      | 秒数,范围为"0"到"59"。                                                                                                                     |
| fffffff | 秒的可选小数部分。 其值的范围为"0000001"（一刻度或一秒的一千万分之一）到"9999999"（一秒的一千万分之九百九十九万九千九百九或一秒少一刻度）。 |

> "c"格式说明符不区分区域性

## 自定义 TimeSpan 格式字符串

任何不是标准 TimeSpan 格式字符串的字符串都会解释为自定义 TimeSpan 格式字符串

> 标准,即预留的字符串关键字  
> 自定义,即原样显示的部分

自定义 TimeSpan 格式说明符不包含占位符分隔符符号,如分隔天与小时、小时与分钟或秒与秒若干分之一的符号。 相反,这些符号必须以字符串形式包含在自定义格式字符串中。 例如,"dd\.hh\:mm" 将句点 (.) 定义为天与小时之间的分隔符,将冒号 (:) 定义为小时与分钟之间的分隔符

自定义 TimeSpan 格式说明符还不包括正负符号,无法区分正负时间间隔。 若要包含正负符号,必须使用条件逻辑构造格式字符串。

```CSharp
using System;

public class Example
{
   public static void Main()
   {
      TimeSpan duration = new TimeSpan(1, 12, 23, 62);

      string output = null;
      output = "Time of Travel: " + duration.ToString("%d") + " days";
      Console.WriteLine(output);
      output = "Time of Travel: " + duration.ToString(@"dd\.hh\:mm\:ss");
      Console.WriteLine(output);

      Console.WriteLine("Time of Travel: {0:%d} day(s)", duration);
      Console.WriteLine("Time of Travel: {0:dd\\.hh\\:mm\\:ss} days", duration);
   }
}
// The example displays the following output:
//       Time of Travel: 1 days
//       Time of Travel: 01.12:24:02
//       Time of Travel: 1 day(s)
//       Time of Travel: 01.12:24:02 days
```
*简单例子*

下表列出了自定义日期和时间格式说明符

* "d","%d"	时间间隔中的整天数。
* "dd"-"dddddddd"	时间间隔中的整天数,根据需要使用前导零填充。
* "h","%h"	时间间隔中不计为天数一部分的整小时数。 一位数小时数没有前导零。
* "hh"	时间间隔中不计为天数一部分的整小时数。 一位数小时具有前导零
* "m","%m"	时间间隔中不包含在小时或天数中的整分钟数。 一位数分钟数没有前导零。
* "mm"	时间间隔中不包含在小时或天数中的整分钟数。 一位数分钟具有前导零。
* "s","%s"	时间间隔中不包含在小时、天数或分钟中的整秒数。 一位数秒数没有前导零。
* "ss"	时间间隔中不包含在小时、天数或分钟中的整秒数。 一位数秒具有前导零。
* "f","%f"	时间间隔中的十分之几秒。
* "ff"	时间间隔中的百分之几秒。
* "fff"	时间间隔中的毫秒。
* "ffff"	时间间隔中的万分之几秒。
* "fffff"	时间间隔中的十万分之几秒。
* "ffffff"	时间间隔中的百万分之几秒。
* "fffffff"	时间间隔中的千万分之几秒（或小数时钟周期）。
* "F","%F"	时间间隔中的十分之几秒。 如果该数字为零,则不显示任何内容
* "FF"	时间间隔中的百分之几秒。 不包含任何小数尾随零或两个零位
* "FFF"	时间间隔中的毫秒。 不包含任何小数尾随零。
* "FFFF"	时间间隔中的万分之几秒。 不包含任何小数尾随零。
* "FFFFF"	时间间隔中的十万分之几秒。 不包含任何小数尾随零。
* "FFFFFF"	时间间隔中的百万分之几秒。 不显示任何小数尾随零。
* "FFFFFFF"	时间间隔中的千万分之几秒。 不显示任何小数尾随零或七个零。
  * TimeSpan.Parse("00:00:06.3291791") => FFFFFF：3291791
  * TimeSpan.Parse("0:0:3.1900000") => ss\.FFFFFF：03.19
* 'string'	文本字符串分隔符。
  * hh':'mm':'ss --> "14:32:17" 
* \	转义字符。
  * hh\:mm\:ss --> "14:32:17"
* 任何其他字符	任何其他未转义字符会解释为自定义格式说明符。
  * hh\:mm\:ss --> "14:32:17"

# 枚举格式字符串

* G 或 g
  * 如果枚举使用 Flags 属性集进行定义,则每个有效项的字符串值会连接在一起（以逗号分隔）。 如果未设置 Flags 属性,则将无效值显示为数字项
```CSharp
Console.WriteLine(ConsoleColor.Red.ToString("G"));         // Displays Red
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("G"));   // Displays Hidden, Archive
```

* F 或 f
  * 如果值可以完全显示为枚举中项的总和（即使未提供 Flags 属性）,则每个有效项的字符串值会连接在一起（以逗号分隔）。 如果值不能由枚举项完全确定,则值会格式化为整数值
```CSharp
Console.WriteLine(ConsoleColor.Blue.ToString("F"));       // Displays Blue
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("F"));   // Displays Hidden, Archive
```

* D 或 d
  * 以尽可能短的表示形式将枚举项显示为整数值
```CSharp
Console.WriteLine(ConsoleColor.Cyan.ToString("D"));         // Displays 11
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("D"));                // Displays 34
```

* X 或 x
  * 将枚举项显示为十六进制值。 根据需要以前导零表示此值,以确保在枚举类型的基础数值类型中,结果字符串的每个字节都有两个字符
```CSharp
Console.WriteLine(ConsoleColor.Cyan.ToString("X"));   // Displays 0000000B
FileAttributes attributes = FileAttributes.Hidden |
                            FileAttributes.Archive;
Console.WriteLine(attributes.ToString("X"));          // Displays 00000022
```
*在示例中,这两者的基础类型 ConsoleColor 和 FileAttributes 为 Int32,或 32 位（或 4 字节）整数,它将生成 8 个字符的结果字符串*

# 复合格式设置

复合格式字符串由固定文本和索引占位符混和组成  

一般像是 `%d, %f` 或者 `{0}, {1}` 这样

> 官方建议在支持的版本上尽量使用`内插字符串`, 即使用`$`开头并直接调用内容`{name}, {arg}`这样

复合格式设置功能受诸如以下方法的支持：

* `String.Format`
  * 常用格式化字符串函数
* `StringBuilder.AppendFormat`
  * 拼接字符串
* `Console.WriteLine` 
  * 控制台显示
* `TextWriter.WriteLine`
  * 写入文件流
  * 派生自 `TextWriter` 的类（如 `StreamWriter` 和 `HtmlTextWriter`）也共享此功能
* `Debug.WriteLine(String, Object[])`
* `Trace.TraceError(String, Object[]), Trace.TraceInformation(String, Object[]) 和 Trace.TraceWarning(String, Object[]) `
* TraceSource.TraceInformation(String, Object[])
  * 都是DeBug信息

====

复合格式字符串由零个或多个固定文本段与一个或多个格式项混和组成

固定文本是所选择的任何字符串,并且每个格式项对应于列表中的一个对象或装箱的结构  
复合格式设置功能返回新的结果字符串,其中每个格式项都被列表中相应对象的字符串表示形式取代

```CSharp
string name = "Fred";
String.Format("Name = {0}, hours = {1:hh}", name, DateTime.Now);
```
*固定文本为"`Name =`"和", `hours =`"*  
*格式项为"`{0}`"和"`{1:hh}`"*

每个格式项都采用下面的形式并包含以下组件：

`{index[,alignment][:formatString]}`

必须使用成对的大括号（"{"和"}"）。

> 可见区分为, 1.索引组件, 2.对齐组件(可选), 3.可选额外格式化参数(参数)

## 索引组件

必需的 

从 0 开始的数字,可标识对象列表中对应的项

```CSharp
string primes;
primes = String.Format("Prime numbers less than 10: {0}, {1}, {2}, {3}",
                       2, 3, 5, 7 );
Console.WriteLine(primes);
// The example displays the following output:
//      Prime numbers less than 10: 2, 3, 5, 7
```
*只有索引组件*

## 对齐组件

可选

带符号的整数, 指示首选的设置了格式的字段宽度

 如果 `alignment` 值小于设置了格式的字符串的长度,alignment 将被忽略,并使用设置了格式的字符串的长度作为字段宽度  
如果 `alignment` 为正数,字段中设置了格式的数据为右对齐；  
如果 `alignment` 为负数,字段中的设置了格式的数据为左对齐  
如果需要填充,则使用空白。 
 
 **如果指定 `alignment`,则需要使用逗号**


```CSharp
string[] names = { "Adam", "Bridgette", "Carla", "Daniel",
                    "Ebenezer", "Francine", "George" };
decimal[] hours = { 40, 6.667m, 40.39m, 82, 40.333m, 80,
                            16.75m };
Console.WriteLine("{0,-20} {1,5}\n", "Name", "Hours");
for (int ctr = 0; ctr < names.Length; ctr++)
    Console.WriteLine("{0,-20} {1,5:N1}", names[ctr], hours[ctr]);
// The example displays the following output:
//       Name                 Hours
//
//       Adam                  40.0
//       Bridgette              6.7
//       Carla                 40.4
//       Daniel                82.0
//       Ebenezer              40.3
//       Francine              80.0
//       George                16.8
```
*输出的内容对齐了*  
*注意逗号的使用*

## 格式字符串组件

可选

指定标准或自定义格式字符串.  
对于数字,日期,时间,枚举,之前说明过的官方预定标准格式字符串都可以使用.

**如果指定 `formatString`,则需要使用冒号**

## 转义大括号

两个表示一个

按照在格式项中遇到大括号的顺序依次解释它们。 不支持解释嵌套的大括号

解释转义大括号的方式会导致意外的结果。 例如,假设格式项为"{{{0:D}}}"  
按照以下方式解释该格式项

1. 前两个左大括号 ("{{") 被转义,生成一个左大括号
2. 之后的三个字符 ("{0:") 被解释为格式项的开始
3. 下一个字符 ("D") 将被解释为 Decimal 标准数值格式说明符,但后面的两个转义大括号 ("}}") 生成单个大括号
   1. 所以的到的整个字符串为`D}`
   2. 不是标准数值格式说明符号
   3. 用于显示字符串`D}`的自定义格式字符串
4. 最后一个大括号 ("}") 被解释为格式项的结束
5. 显示的最终结果是字符串"{D}"。 不会显示本来要设置格式的数值

在编写代码时,避免错误解释转义大括号和格式项的一种方法是单独设置大括号和格式项的格式

```CSharp
int value = 6324;
string output = string.Format("{0}{1:D}{2}",
                             "{", value, "}");
Console.WriteLine(output);
// The example displays the following output:
//       {6324}
```

> 前面(开头代码)已经讲过复杂格式的内部怎么运作了  
> 实际都是自定义格式

# 处理顺序

激动人心的工作流终于来了  
一下内容可能涉及到官方实现的自定义格式字符串,手动定义可能更改一些结果,比如可接受`Null`


如果对复合格式设置方法的调用包括其值*不为* `null` 的 `IFormatProvider` 参数   
则运行时会*调用*其 `IFormatProvider.GetFormat` 方法来请求 `ICustomFormatter` 实现  
如果此方法能够返回 `ICustomFormatter` 实现,那么它将在复合格式方法调用期间*缓存*

如果要设置格式的值为 `null`,则将返回空字符串 `String.Empty`

如果 `ICustomFormatter` 实现可用,则运行时将调用其 Format 方法。 它向方法传递格式项的 `formatString` 值（若有）或 `null`（若无）以及 `IFormatProvider` 实现  
*此时就进入了自定义处理流程*

如果对 `ICustomFormatter.Format` 方法的调用返回 `null`,则继续执行下一步骤,将返回 `ICustomFormatter.Format` 调用的结果

如果该值实现 `IFormattable` 接口,则调用此接口的 `ToString(String, IFormatProvider)` 方法

如果格式项中存在 `formatString` 值,则向方法传递该值；如果不存在该值,则传递 `null`  
按如下方式确定 `IFormatProvider` 自变量

* 对于数字,日期和时间,枚举,都是用官方给定的`IFormatProvider`类
* 对于其他类型的对象,如果调用带 `IFormatProvider` 参数的复合格式设置方法,它的值会直接传递到 `IFormattable.ToString` 实现,这个值可能为`null`

随着几项内容都为null,最终的保底方案是`Object.ToString()`方法,他不接受任何参数,如果没有`Object`,那么返回`String.Empty`

# 总结

全篇看完,知识细讲了很多官方预留的标准格式化字符串,具体流程其实就在开头的代码里.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
