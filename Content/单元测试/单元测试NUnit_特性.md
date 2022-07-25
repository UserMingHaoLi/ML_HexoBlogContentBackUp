---
title: 单元测试NUnit_特性
date: 2021-03-26 23:33:00
updated: 2021-03-26 23:33:00
id: ml-20210326-233300-g103
categories:
	- 基础
	- 单元测试
tags: 
	- 基础
	- 单元测试
	- CSharp
---

# 特性

特效类似于这样`[Test]`,是一种C#语法,为代码添加额外的元数据  

<!--more-->

## Apartment

定测试应在特定单元（STA 或 MTA）中运行

既多线程模式或单线程模式

如果未指定此属性，测试将在 MTA 中运行

## Author

作者, 包含Name和eMail参数

每个测试可以有多个作者

## Category

类别

某些启动测试的指令可能会运行指定分组

对于`SetUpFixture`, 分组不起作用

此类型可以继承, 用于自定义你项目的高级类别

## Combinatorial
## Culture
## Datapoint
## DatapointSource
## DefaultFloatingPointTolerance
## Description
## Explicit
## FixtureLifeCycle
## Ignore
## LevelOfParallelism
## MaxTime
## NonTestAssembly
## OneTimeSetUp
## OneTimeTearDown
## Order
## Pairwise
## Parallelizable
## NonParallelizable
## Platform
## Property
## Random
## Range
## Repeat
## RequiresThread
## Retry
## Sequential
## SetCulture
## SetUICulture
## Setup
## SetUpFixture
## SingleThreaded
## TearDown
## Test
## TestCase
## TestCaseSource
## TestFixture
## TestFixtureSetUp
## TestFixtureSource
## TestFixtureTearDown
## TestOf
## Theory
## Timeout
## Values
## ValueSource

## Category

**分组**  

他有一个string的参数, 你可以指定一个方法的组名.  
这样就可以在视图中使用分组依据-特征. 来按照你规定的string显示分组. 任意组合并运行他们

可以用于类, 这样整个类都是该类型.

*如果一个方法有多个分组, 那么它显示多次, 但实际上还是只执行了一次.*

## Explicit

**跳过**

除非手动选定, 否则不会执行. 可以用于较为耗时的方法.

## SetUp

**预处理**

此方法在每个测试开始前运行一次

## TearDown

**收尾**

对应上面, 但这个是每次结束运行一次

## OneTimeSetUp

**预处理**

类级别的Up, 在每个类初始化的时候调用一次.

## OneTimeTearDown

**收尾**

同上, 类完成的时候结束一次.

## Igonre

**忽略**

不执行这个测试, 可以用于正在编写实现代码的测试.  
虽然你可以不使用这个特性, 因为你知道这个代码不会通过, 但是无论如何, 不要养成忽略失败测试的习惯, *每个测试都应该保持通过状态*, 不然单元测试就失去了意义.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
