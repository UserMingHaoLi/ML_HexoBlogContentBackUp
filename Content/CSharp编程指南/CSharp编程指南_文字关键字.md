---
title: CSharp编程指南_文字关键字
date: 2021-04-25 22:14:00
updated: 2021-04-25 22:14:00
id: ml-20210425-221400-g131
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

* null
* true
* false
* default

<!--more-->

# null

`null` 关键字是表示不引用任何对象的空引用的文字值

`null` 是引用类型变量的默认值

> 但是`null`不是引用类型,他只是表示该变量没有任何值.

# true和false

`bool` 类型关键字是 .NET `System.Boolean` 结构类型的别名，它表示一个布尔值，可为 `true` 或 `false`

可使用 `true` 和 `false` 文本来初始化 `bool` 变量或传递 `bool` 值

# default

* 指定 `switch` 语句中的默认标签。
* 作为 `default` 默认运算符或文本生成类型的默认值。

```CSharp
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

```CSharp
int a0 = default;
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
