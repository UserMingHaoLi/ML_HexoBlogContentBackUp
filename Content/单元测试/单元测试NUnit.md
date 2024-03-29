---
title: 单元测试NUnit
date: 2021-03-24 20:56:00
updated: 2021-03-27 21:36:00
id: ml-20210324-205600-g101
categories:
	- 基础
	- 单元测试
tags: 
	- 基础
	- 单元测试
	- CSharp
---

之所以选择`NUnit`是因为`Unity`的测试也是这玩意.  
这是开源的,可以在`GitHub`找到它的源码.  
本文为阅读`单元测试之道`后编写.

> 下面是比较好的单元测试文档地址

[NUnit文档地址][NUnitDoc_Link]  
[.NET Core 指南][DotNetCoreUTDoc_Link]  
[Visual Studio 文档][VisualStudioUTDoc_Link]  
[UnityTestFramework 文档][UnityTestFrameworkDoc_Link]  

<!--more-->

# 安装NUnit

虽然可以使用vs自带的`MSTest`, 但是NUnit也是不错的选择.  
在NuGet包管理器中搜索安装`NUnit`, `NUnit3TestAdapter` 这样就可以在创建单元测试的时候使用NUnit了

# 忽略文件

[忽略文件, 用于Visual Studio 获取最新版本][VisualStudio.gitignore_Link] 

# 快速开始

安装好两个包.  
点击解决方案,添加,搜索`NUnit`,并选择Nunit测试项目.

> 命名应该和要测试的程序集同名,并在后方加上`Test`或者`_Test`.这样他们就能在名称排序中紧挨在一起,方便查找.

在单元测试项目中,右键单击"引用"或"依赖项",然后选择"添加引用",添加对要测试的项目的引用

之后打开视图,测试资源管理器.  
点击运行全部测试,看到全部通过.  

之后就随意添加单元测试吧.

> 可以直接添加MSTest来完成上面步骤

之后将MSTest的`Using`替换为`using NUnit.Framework;`

使用`[TestFixture()]`, `[Test()]`来处理类型和方法

报错`testhost.dll`则是因为没有安装`testhost`的NuGet库

> **每次命名都要有意义**

> 安装[快速生成Nunit测试插件](https://marketplace.visualstudio.com/items?itemName=NUnitDevelopers.TestGeneratorNUnitextension-18371)可以通过右键方法快速生成测试模板,但是不安装也可以通过上面的方式添加.

## 结果是否正确

在开发过程中,文档或者需求往往不能完全准确.  
按自己的世界观填充其中的模糊需求,以求立刻开始编码.等到文档逐步完善再逐步修改.

> 这样就可以有一份至少在自己世界观内正确的文档,从而可以推导出正确的结果.用于方便编写测试  
> 无论如何,至少让代码做的事和预期一样(即使预期是假想的).

**检测外部数据文件是否符合规范**

比起完善的代码单元测试, 外部数据变化和错误的概率明显更高.也更需要测试.

## 边界是否检查过了

很好理解, 需要参数的极限值, 比如 0个,1个,Max个  
或者实际参数的边界值 Max Min什么的.

> 例子

* 完全伪造的**不可能数据**, 比如一串乱码.
* **错误的格式**, 比如没有结尾的邮箱号.
* **空值**或者**部分空值**, 这里包含"",0,null
* 一些代码上合理单**逻辑不合理**的数据, 比如年龄=10000
* 输入**明显不满足**的数据, 比如要求一个连续数字, 给一个不连续的.
* **颠覆顺序**, 比如没有登录就尝试消费.

需要满足以下内容

* 和预期一致的或非一致的
* 顺序的或乱序的
* 引用外部代码的
* 不存在的
* 范围内地和范围外的
* 不满足基本条件的
* 满足顺序和不满足顺序的

## 检查反向内容

如果向数据库插入一条数据, 需要查询数据库来校验是否成功.

> 此时不应该使用自己的代码, 或许可以尝试原生的无错代码, 以免*双向错误*.  
> 也可以使用经过验证的代码,但是记住,**一次只修改其中的一项**.

## 交叉检查结果

通常有多重方法满足条件, 为一种编写测试, 用另一种验证结果.

## 是否拥有强制错误抛出

是否有异常处理机制.

* 网络异常
* 磁盘异常(IO)
* 时钟问题
* 内存占用问题
* 分辨率问题

> 基本的Null只是小儿科

## 是否满足性能要求

运行时间如何?

有适合一个算法可能因为输入增多而指数级的消耗性能. 可能需要编写一些针对性的测试.  
这些测试通常都很消耗性能或者时间, 可以将他们归类, 之后每天晚上(每周)测试一次.


## 边界条件

* 任何字符串都可能不符合预期,甚至可能被恶意注入
* 任何索引都应该被大量测试.包括-1,0,Max,Max+1.或者其他边界值.
  * 如果是复数索引,那就更需要注意.
* 对于一个区间数据,不应该使用原生数据类型,应该包装起来以便随时检测他们是否符合预期
* 检测状态.
* 依赖的对象存在吗?是否可能是一个错误的类型或者数据.
* 看起来顺序的掉用真的能一直这么顺利吗?


## 结尾

**最好的编写测试方法是, 编写一个测试后, 询问自己还有什么可能错.**

*如果有一个重要的功能是非公开的, 但一定要测试的, 重构而不是暴露隐私

*一个方法存在于多个分组中,只会执行一次.另外的分组只是引用成功或者失败的结果*

*使用`IntelliTest`可以让代码生成单元测试,具体请看前文的文档*

*可以使用标签声明分组,也可以在可视化界面中选择并创建分组.但是无论怎么分组.*


# 待续

Log

共享上下文

Propchange(属性变化代理)

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[NUnitDoc_Link]:https://docs.nunit.org/articles/nunit/intro.html "NUnitDoc"
[DotNetCoreUTDoc_Link]:https://www.bookstack.cn/read/dotnet/7bc375a213bb0ecc.md ".NetCore单元测试文档"
[VisualStudioUTDoc_Link]:https://docs.microsoft.com/zh-cn/visualstudio/test/unit-test-basics?view=vs-2019 "VisualStudio单元测试文档"
[UnityTestFrameworkDoc_Link]:https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/index.html "Unity单元测试文档"

[VisualStudio.gitignore_Link]:https://github.com/github/gitignore/blob/master/VisualStudio.gitignore "VisualStudio 忽略文件"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
