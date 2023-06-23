---
title: CSharp4.0
date: 2021-04-01 00:28:00
updated: 2021-04-01 00:28:00
id: ml-20210401-002800-g109
categories:
	- 基础
	- CSharp版本更新
tags: 
	- 基础
	- CSharp版本更新
	- CSharp
---

- [C#4.0](#c40)
	- [动态绑定](#动态绑定)
	- [命名参数/可选参数](#命名参数可选参数)
		- [命名参数](#命名参数)
		- [可选参数](#可选参数)
	- [泛型协变和逆变](#泛型协变和逆变)
	- [嵌入的互操作类型](#嵌入的互操作类型)
- [完毕](#完毕)


<!--more-->

[toc]

# C#4.0

## 动态绑定

`dynamic`关键字允许你实现动态绑定, 就像js一样

使用该关键字修饰的参数不会再编译时验证数据类型, 只在运行时转化为相应的类型.

dynamic 的实现是基于`IDynamicObject`接口和`DynamicObject`抽象类.而动态方法、属性的调用都被转为了`GetMember`、`Invoke`等方法的调用.  
如果想在自己的代码中实现一个动态类型对象,可以继承`DynamicObject`类,并实现自己的若干get和set方法.	

> `DLR(Dynamic Language Runtime)`提供了`Microsoft.CSharp.RuntimeBinder`命名空间

动态绑定存在出错的可能性，不过同时也为你提供了强大的语言功能。

## 命名参数/可选参数

### 命名参数

```
//常规传参
PrintOrderDetails("Gift Shop", 31, "Red Mug");
//命名实参
PrintOrderDetails(orderNum: 31, productName: "Red Mug", sellerName: "Gift Shop");
```

你可以通过指定参数名称来无视参数顺序, 这样也更精准, 并为接下来的可选参数做准备.

### 可选参数

声明方法的时候给方法指定默认值, 在调用的时候就可以选择不赋值这个参数.  
参数只能是值类型, 不能指定默认引用类型, 但可以是`null`

```
public void ExampleMethod(int required, string optionalstr = "default string",int optionalint = 10)
```

如果你需要使用`optionalstr`的默认值, 单想要对`optionalint`赋值, 这就需要用到命名参数.

```
anExample.ExampleMethod(3, optionalint: 4);
```

> 很多编译器(插件)已支持对非命名参数显示参数名称, 填写参数时自动使用命名参数等功能

## 泛型协变和逆变

上次讲了.

其实,做这些本质上都是要在保证运行时类型安全的前提下提高代码的可重用性和灵活性

> 要理解这些,只要记住**宽进严出**, 进入参数可以使用子类,出去的返回值要明确.

有规则, 有限制, 才能收束,预测, 然后才能优化  
如果所有类型都是obj, 甚至是直接是二进制, 各种开发效率提升, 性能优化都无从谈起

## 嵌入的互操作类型

//文档看不懂

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
