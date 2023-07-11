---
title: Unity和DotNet生态
date: 2023-07-11 22:35:00
updated: 2023-07-11 22:35:00
id: ml-20230711-223500-g194
categories:
	- Unity
	- C#
tags: 
	- Unity
	- C#
---


<!--more-->

# 什么是.Net

.Net 是一套规范(接口), 定义了其中的工具, 编程语言, 库, 运行时, 跨平台等相关规范, 目前为止, .Net已有很多成熟的实现并形成可供开发人员开发的`.Net开发人员平台`

.NET 是一个开发人员平台，由工具、编程语言、库组成，用于构建许多不同类型的应用程序

.NET定义了一些功能, 比如
- 异步代码
- 属性
- 反射
- 代码分析器
- 委托和 lambda
- 事件
- 异常
- 垃圾回收
- 泛型类型
- LINQ（语言集成查询）。
- 并行编程 和 托管线程
- 类型推理 - C#、F#、Visual Basic。
- 类型系统
- 不安全代码

`公共语言运行时(CLR)` 支持.Net功能的运行时环境, 并有如下功能
- 垃圾回收。
- 内存安全和类型安全。
- 对编程语言的全面支持。
- 跨平台设计

编译为`中间语言 (IL)` , 任何支持.Net的语言, 最终需要支持编译为中间语言 (IL)  
IL是一种紧凑的代码格式，可在任何操作系统或体系结构上受到`公共语言运行时(CLR)`的支持  
IL 需要编译为本机代码，以便在 CPU 上执行，例如 Arm64 或 x64。 .NET 支持提前 `AOT` 和实时 `JIT` 编译模型
- 在 `Android`、`macOS` 和 `Linux` 上，JIT 编译是默认的，AOT 是可选的（例如，使用 ReadyToRun）。
- 在 `iOS` 上，除非在模拟器中运行，否则 `AOT` 是必需的。
- 在 `WebAssembly (Wasm)` 环境中，`AOT` 是必需的。

`JIT` 的优势在于，它可以根据基础操作系统和硬件，在给定的环境中将应用（未修改）编译为 CPU 指令和调用约定。 它还可以在更高或更低的质量级别编译代码，以实现更好的启动和稳定状态吞吐量性能。

`AOT` 的优势是，它提供最佳的应用启动性能，并且在某些情况下，可以使部署更小。 主要缺点是，必须为每个单独的部署目标生成二进制文件（与任何其他本机代码相同）。 AOT 代码与某些反射模式不兼容

## .NET 的实现

> 定义了接口就有实现, 很合理

.NET 有多个变体，每个变体都支持不同类型的应用。 有多个变体的原因一部分是历史信息相关、一部分是技术相关。

.NET 实现（历史顺序）：

- .NET Framework - 它提供对 Windows 和 Windows Server 的广泛功能的访问权限。 此外，它还广泛用于基于 Windows 的云计算。 是原始 .NET。
- Mono - 一个跨平台 .NET Framework 实现。 原始社区和开放源代码 .NET。 用于 Android、iOS 和 Wasm 应用。
- .NET (Core) - .NET 的一个跨平台开放源代码实现，已针对云时代进行了反思，同时与 .NET Framework 保持极大的兼容。 用于 Linux、macOS 和 Windows 应用


.NET 的每个实现都具有以下组件：

- 一个或多个运行时，例如 .NET Framework CLR 和 .NET 5 CLR。
- 类库，例如 .NET Framework 基类库和 .NET 5 基类库。
- （可选）一个或多个应用程序框架，例如 ASP.NET、Windows Forms 和 Windows Presentation Foundation (WPF) 都包括在 .NET Framework 和 .NET 5+ 中。
- 可包含开发工具。 某些开发工具在多个实现之间共享

Microsoft 支持以下四种 .NET 实现：

- .NET 5+（以前称为 .NET Core）
  - 是 .NET 的跨平台实现，专用于处理大规模的服务器和云工作负载。 它还支持其他工作负载，包括桌面应用。 可在 Windows、macOS 和 Linux 上运行。 它可实现 .NET Standard，因此面向 .NET Standard 的代码都可在 .NET 5+ 上运行
- .NET Framework
  - .Net Framework 是自 2002 年起就已存在的原始 .NET 实现。 4\.5 版以及更高版本实现 .NET Standard，因此面向 .NET Standard 的代码都可在这些版本的 .NET Framework 上运行。 它还包含一些特定于 Windows 的 API，如通过 Windows 窗体和 WPF 进行 Windows 桌面开发的 API。 .NET Framework 非常适合用于生成 Windows 桌面应用程序
- Mono
  - Mono 是主要在需要小型运行时使用的 .NET 实现。 它是在 Android、macOS、iOS、tvOS 和 watchOS 上驱动 Xamarin 应用程序的运行时，且主要针对小内存占用。 Mono 还支持使用 Unity 引擎生成的游戏。它支持所有当前已发布的 .NET Standard 版本, Mono 通常与实时编译器一起使用，但它也提供在 iOS 之类的平台使用的完整静态编译器（预先编译）
- UWP
  - UWP 是用于为物联网 (IoT) 生成新式触控 Windows 应用程序和软件的 .NET 实现。 它旨在统一可能想要以其为目标的不同类型的设备，包括电脑、平板电脑、电话，甚至 Xbox。 UWP 提供许多服务，如集中式应用商店、执行环境 (AppContainer) 和一组 Windows API（用于代替 Win32 (WinRT)）

> .NET 7 目前是主要实现，也是持续开发的重点

## .NET 类库

类库是 .NET 的共享库概念。 通过类库可将实用功能组件化为可供多个应用程序使用的模块

有三种类型的类库可供使用：

- 平台特定的类库可访问给定平台（例如，Windows 上的 .NET Framework、Xamarin、iOS）中的所有 API，但只有面向该平台的应用和库可使用该类库。
- 可移植类库可访问 API 的子集，并且可供面向多个平台的应用和库使用。
- .NET Standard 类库将平台专用库概念和可移植库概念合并到一个模型中，以同时获取两方面的优势。
  - .NET Standard 不公开整个 .NET Framework 的功能（也不将此作为目标），但相比可移植类库，库公开的 API 更多

## Ecma 标准

C# 语言和公共语言基础结构 (CLI) 规范通过 Ecma International® 进行标准化。 这些标准的第一版已于 2001 年 12 月由 Ecma 发布

以下官方 Ecma 文档可用于 C# 和 CLI (TR-84)：

-C# 语言标准（版本 6.0）：ECMA-334.pdf
-公共语言基础结构：ECMA-335.pdf。
-派生自分区 IV XML 文件的信息：ECMA-084.pdf 格式。

官方 ISO/IEC 文档可从 ISO/IEC 公开标准页获取。 以下链接可从该页面直接获得：

-信息技术 - 编程语言 - C# ：ISO/IEC 23270:2018
-信息技术 - 公共语言基础结构 (CLI) 分区 I 到 VI：ISO/IEC 23271:2012
-信息技术 - 公共语言基础结构 (CLI) - 有关派生自分区 IV XML 文件的信息的技术报告：ISO/IEC TR 23272:2011

## .Net实现的构成(部分)

**运行时库**

这些库提供最基本的类型和实用工具功能，是其他所有 .NET 类库的基础。 例如 `System.String` 类，该类提供了用于处理字符串的 API。 另一个示例是序列化库

某些库在 `NuGet` 包中提供，而不是包含在运行时的共享框架中。 例如：

| 概念内容     | NuGet 包                                 |
| ------------ | ---------------------------------------- |
| 配置         | Microsoft.Extensions.Configuration       |
| 依赖关系注入 | Microsoft.Extensions.DependencyInjection |
| 文件通配     | Microsoft.Extensions.FileSystemGlobbing  |
| 泛型主机     | Microsoft.Extensions.Hosting             |
| HTTP         | Microsoft.Extensions.Http               |
| 本地化       | Microsoft.Extensions.Localization        |
| Logging      | Microsoft.Extensions.Logging             |


**工具**

一组用于开发和运行 .NET 应用程序的库和工具

-  MSBuild 生成引擎
-  Roslyn（C# 和 Visual Basic）编译器
-  dotnet 命令
-  CLI 工具
-  Visual Studio 系列 IDE
-  GitHub Codespaces 和 GitHub 安全功能支持 .NET

**语言**
- C#
- F#
- VB

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
