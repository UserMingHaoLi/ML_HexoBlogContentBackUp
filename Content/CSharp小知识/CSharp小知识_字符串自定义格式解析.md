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

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
