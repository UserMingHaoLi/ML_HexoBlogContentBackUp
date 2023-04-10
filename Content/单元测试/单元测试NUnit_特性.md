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

如果未指定此属性,测试将在 MTA 中运行

## Author

作者, 包含Name和eMail参数

每个测试可以有多个作者

## Category

类别

某些启动测试的指令可能会运行指定分组

对于`SetUpFixture`, 分组不起作用

此类型可以继承, 用于自定义你项目的高级类别

继承类的默认受保护构造函数将类别名称设置为类的名称.

需要运行过一次之后, NUnit才会在UI中展示其自定义特征名称.

## Combinatorial

Combinatorial

组合

以指定 NUnit 应为为测试参数提供的各个数据项的所有可能组合生成测试用例

```CSharp
[Test, Combinatorial]
public void MyTest(
    [Values(1, 2, 3)] int x,
    [Values("A", "B")] string s)
{
    ...
}

//MyTest 被调用六次,如下所示：

MyTest(1, "A")
MyTest(1, "B")
MyTest(2, "A")
MyTest(2, "B")
MyTest(3, "A")
MyTest(3, "B")
```

在泛型方法上使用时,程序员必须确保所有可能的参数组合都是有效的。  
当多个参数使用相同的泛型类型（例如：T）时,这可能是不可能的,  
并且该属性可能会生成无效的测试用例

## Culture

Culture 文化区域

如果未满足测试的指定区域性要求,则跳过该要求。在 gui 中,测试的树节点保持灰色,状态栏颜色不受影响

如果您希望在运行测试时更改区域性,请改用 SetCulture 属性

```CSharp
[TestFixture]
[Culture("fr-FR")]
public class FrenchCultureTests
{
// ...
}
```

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
