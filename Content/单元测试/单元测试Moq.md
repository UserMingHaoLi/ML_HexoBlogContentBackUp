---
title: 单元测试Moq
date: 2021-03-24 20:56:01
updated: 2021-03-24 20:56:01
id: ml-20210324-205601-g102
categories:
	- 基础
	- 单元测试
tags: 
	- 基础
	- 单元测试
	- CSharp
---

单元测试时使用.

<!--more-->

# Moq

可以通过vs自带的NuGet直接安装

使用的时候只能通过接口或者虚方法(建议使用接口).

## 声明

`Mock<T> mock = new Mock<T>();`

> 获得值

`T t = mock.Object`

## 模拟方法

`mock.Setup(f => f.function()).Returns((int i)=> ++i)`

这里为原本什么都没有的Mock对象接入了接口中的function函数, 并设置返回值为function的返回值在加1;

方法是`Setup`  
第一个表达式是你需要重写的Mock方法, 必须是是T类型的一个虚方法.  
后面的`Returns`会获得上面方法的真实返回值, 前提是他有返回.  
之后你可以定义一句语句来返回你想要的值.

你可以在这个链条中间插入一些`Callback(Action)`, 来进行之前或之后的处理动作.

或者你也可以不返回, 直接使用`Throws<T>()`来抛出一个异常

## 参数匹配

你可以定义一些表达式来筛选相应的参数.  

`mock.Setup(foo => foo.Add(It.Is<int>(i => i % 2 == 0))).Returns(true);`

这个表达式筛选2的倍数, 之后返回true.  

还可以使用`ItIsInRange<T>(T a, T b, Range range)`筛选一个范围

或者`ItIsRegex()`使用一个正则表达式

## 属性

属性和方法并无两样, 只是少了一个小括号而已

`mock.Setup(foo => foo.Name).Returns("bar");`

`mock.Setup(foo => foo.Bar.Baz.Name).Returns("baz");` 可以设置多层应该是一个小亮点.

## 验证

验证你的Mock有没有起到效果.

使用`Verify`

```
// 在验证失败的时候,提供自定义的错误提示信息 
 mock.Verify(foo => foo.Execute("ping"), "When doing operation X, the service should be pinged always");  
```

如果你改动后面的参数, 条件会变动

```
// 从没有被调用的方法 
mock.Verify(foo => foo.Execute("ping"), Times.Never());  
// 至少调用过一次 
mock.Verify(foo => foo.Execute("ping"), Times.AtLeastOnce());  
```

## Mock全局属性

你可以在创建Mock的时候设置初始化动作. 

`var mock = new Mock<IFoo>(MockBehavior.Strict);` 这是严格模式, 如果没有Mock会异常

`CallBase = true` 这回导致调用基类.

`DefaultValue = DefaultValue.Mock` 递归的创建和返回Mock, 所有变量都是Mock的

> 你可以创建一个Mock管理器, 用管理器来创建新的Mock, 他们都拥有你之前设置的行为

```
var factory = new MockFactory(MockBehavior.Strict) { DefaultValue = DefaultValue.Mock };  

// 创建 Mock 对象
var fooMock = factory.Create<IFoo>();  

// 在创建的时候重写仓库的设置 
var barMock = factory.Create<IBar>(MockBehavior.Loose);  
```

## 接口

使用`As`方法来转换一个接口

```
// 实现多个接口 
var foo = new Mock<IFoo>(); 
var disposableFoo = foo.As<IDisposable>(); 
disposableFoo.Setup(df => df.Dispose());
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->


<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"

