---
title: HybridCLR_阅读文档
date: 2023-06-03 17:39:00
updated: 2023-06-03 17:39:00
id: ml-20230603-173900-g189
categories:
	- Unity
	- 热更新
	- HybridCLR
tags: 
	- Unity
	- 热更新
	- HybridCLR
---

以下链接通往该项目的GitHub

[Code Philosophy](https://github.com/focus-creative-games)

<!--more-->

# 支持的Unity版本

- 2019.4.x
- 2020.3.16 以上
- 2021.3.x
- 2022.3.x

> 推荐安装2019.4.40、2020.3.26+、2021.3.x、2022.3.x版本

# 支持的平台

- Windows x86、x64
- MacOS x86、x64、arm64(silicon)
- Android armv7、armv8(arm64)
- iOS arm64
- WebGL 标准WebGL、MiniGame、微信小游戏
  - 需要详细阅读文档, 有较多特殊操作

# HybridCLR代码结构及版本

完整的HybridCLR代码由三个仓库构成：

- il2cpp_plus
  - 对原始il2cpp代码作少量调整，以支持混合运行模式
- hybridclr
  - 解释器的核心代码
- com.focus-creative-games.hybridclr_unity
  - Unity Package, 运行时代码及编辑器工具

# 安装HybridCLR

安装支持的Unity版本, 并额外安装 `Windows Build Support(IL2CPP)`

安装`visual studio 2019`或更高版本。安装时至少要包含 `使用Unity的游戏开发` 和 `使用c++的游戏开发` 组件

下载并安装`com.focus-creative-games.hybridclr_unity`  
`v3.0.0`及以后版本, 移除了对Unity 2019和2020的支持, 且更名为`com.code-philosophy.hybridclr`  
使用`Package Manager`从git安装, 或下载本地后移动至Packages目录即可

点击菜单 `HybridCLR/Installer` 来初始化, 无法执行时, 可选本地文件  

# com.focus-creative-games.hybridclr_unity 包内容

## HybridCLR菜单介绍

### Installer

- 从Editor安装目录复制il2cpp目录到项目
- 从clone il2cpp_plus和hybridclr仓库，制作出最终的libil2cpp目录
  - 使用本地最终libil2cpp则不执行
- 用最终的libil2cpp目录升级刚刚复制的il2cpp
- 从Editor安装目录复制 `MonoBleedingEdge` 目录到项目
- 其他处理。如2019版本将 Editor的`Unity.IL2CPP.dll` 复制到项目

> 如需全局替换, 详细看官方文档

### Compile Dll

使用目标平台编译开关下编译出的热更新dll

### Generate/Il2CppDef

命令生成了相关版本宏及其他必须的代码

### Generate/LinkXml

扫描热更新dll引用的AOT类型，生成`link.xml`

### Generate/AotDlls

生成裁剪后的AOT dlls

请依次运行以下命令：

* `HybridCLR/Generate/Il2CppDef`
* `HybridCLR/Generate/LinkXml`
* `HybridCLR/Generate/AotDlls`

### Generate/MethodBridge

根据当前的AOT dlls扫描生成桥接函数文件

需已有`Generate/AotDlls`的生成结果

### Generate/AOTGenericReference

根据当前热更新dll扫描出所有产生的AOT泛型类型及函数的实例化，并生成一个启发的泛型实例化文件

包含可能需要开发人员处理的 
* 补充元数据的assembly列表
* 泛型类型及函数的实例化引用

> 本身无效果, 仅供参考

### Generate/ReversePInvokeWrapper

`HybridCLR+lua/js/python`时候需要

### Generate/All

一键执行打包前必须的生成操作。

* Compile Dll
* Il2CppDef
* LinkXml
* AotDlls
* MethodBridge
* ReversePInvokeWrapper
* AOTGenericReference

## HybridCLR 配置

### enable

是否开启HyridCLR热更

### useGlobalIl2cpp

是否使用全局安装

### hybridclrRepoURL
### il2cppPlusRepoURL

仓库地址

### hotUpdateAssemblyDefinitions
### hotUpdateAssemblies

热更新模块列表, 一个是Unity asmdef 形式, 另一个是Dll形式  
合并成最终列表

### preserveHotUpdateAssemblies

预留的热更新dll名字列表, 未来可能新增的热更DLL

### hotUpdateDllCompileOutputRootDir

编译后的热更新dll的输出根目录。最终输出目录在该目录的平台子目录下，即 `${hotUpdateDllCompileOutputRootDir}/{platform}`

### externalHotUpdateAssemblyDirs

编译出的dll在Unity外部, 这里提供热更新dll的搜索路径

### strippedAOTDllOutputRootDir

裁剪后的AOT dll的暂存目录。最终目录在该目录的平台子目录下，即 `${strippedAOTDllOutputRootDir}/{platform}`

### patchAOTAssemblies

补充元数据AOT dlls列表  
插件本身没有用到这个配置项

开发者自行使用

### outputLinkFile

运行菜单`HybridCLR/Generate/LinkXml`命令时，输出的`link.xml`文件路径

### outputAOTGenericReferenceFile

运行菜单`HybridCLR/Generate/AOTGenericReference`时输出的AOT泛型实例化集合文件的路径

### maxGenericReferenceIteration

运行菜单`HybridCLR/Generate/AOTGenericReference`时，生成工具递归分析AOT泛型实例化的迭代次数

推荐=10

### maxMethodBridgeGenericIteration

运行菜单`HybridCLR/Generate/MethodBridge`时，生成工具递归分析AOT泛型实例化的迭代次数

## Build Pipeline相关脚本

* 设置或者清除`UNITY_IL2CPP_PATH`环境变量
* 关闭增量式GC(Use Incremental GC) 选项
* `Scripting Backend` 切换为 `il2cpp`
* 错误配置检测
* 打包时自动排除热更新assembly
  * 显然不应随包编译
* 打包时将热更新dll名添加到assembly列表
  * Unity的资源管理系统才能正确识别和还原热更新脚本
* 备份裁剪后的AOT dll

## iOSBuild脚本

iOS打包支持

## Runtime相关脚本

运行时支持

## LoadImageErrorCode

错误码

## 补充元数据

处理补充元数据的DLL, 可用打包时裁剪后的, 或全量

## ReversePInvokeWrapperGenerationAttribute

lua使用

# 创建和配置热更新程序集

使用asmdef或者dll

很显然，项目代码必须合理拆分为`AOT`（即编译到游戏主包内）程序集 和 `热更新程序集`，才能进行热更新

常见的拆分方式有几种：

* Assembly-CSharp作为AOT程序集。剩余代码自己拆分为N个AOT程序集和M个热更新程序集。
* Assembly-CSharp作为热更新程序集。剩余代码自己拆分为N个AOT程序集和M个热更新程序集。

不要在AOT程序集中引用热更新程序集，否则会导致打包出错

# 编译 热更新assembly

热更新代码中有可能会包含宏开关，因此每个平台需要单独编译热更新assembly。

运行菜单`HybridCLR/Compile/xxx`命令直接编译出热更新dll  
`HybridCLR/Generate/All`时会也隐含编译最新的热更新程序集


提供了`HybridCLR.Editor.Commands.CompileDllCommand.CompileDll(BuildTarget target)`接口， 方便开发者灵活地自行编译热更新dll

发布主包后，每次热更新时只需要简单使用`HybridCLR/Compile/xxx`命令重新编译热更新dll，再发布热更新dll即可

# 加载和使用热更新代码

要按照依赖顺序加载

* 通过反射运行
* 通过Unity的AddComponent运行
* 通过加载ab内的Prefab和Scene运行其上的Mono脚本
  * **推荐**

# 打包流程

1. 运行菜单 `HybridCLR/Generate/All` 一键执行必要的生成操作
2. 将`HybridCLRData/HotUpdateDlls`下的热更新dll添加到项目的热更新资源管理系统
3. 将`HybridCLRData/AssembliesPostIl2CppStrip`下的补充元数据 dll添加到项目的热更新资源管理系统
4. 项目的正常打包流程

编译iOS程序时需要自己单独编译libil2cpp.a，再替换xcode工程的libil2cpp.a文件，接着再打包

- 运行 `HybridCLR/Generate/All` 生成所有必要的文件
- 打开命令控制台，切换到 `{project}/HybridCLRData/iOSBuild` 目录。路径不能包含空格
- bash ./build_libil2cpp.sh 编译libil2cpp.a 。运行结束后，如果在`iOSBuild/build`目录下能找到libil2cpp.a文件并且size大于60M，表示编译成功

# 代码裁剪问题

`HybridCLR/Generate/LinkXml`  确保在主工程代码中显式引用过其中的类或函数

不能预知未来将来使用的类型。因此仍然需要有规划地提前在`Assets/link.xml`注册

# AOT泛型问题

il2cpp支持

从类型, 泛型T, 参数, 全路径使用的均为引用类型时, 可以共享  
部分路径相同则部分共享

> 这是由于值类型对齐以及寄存器的使用不同

HybridCLR提供补充元数据技术来生成相关代码   
使用`HybridCLR.RuntimeApi.LoadMetadataForAOTAssembly`函数即可  
实践中比较合理的时机是热更新dll加载后但还未执行任何代码前

注意补充的本体, 以`List<YourValueType>.Add`为例   
应该补充`List<T>`所在dll（即`mscorlib`）的元数据

一些C#特殊机制引发的AOT泛型问题  
以async为例，编译器为async生成了若干类及状态机及一些代码，这些隐藏生成的代码中包含了对多个AOT泛型函数的调用，常见的有：

- `void AsyncTaskMethodBuilder::Start<TStateMachine>(ref TStateMachine stateMachine)`
- `void AsyncTaskMethodBuilder::AwaitUnsafeOnCompleted<TAwaiter, TStateMachine>(ref TAwaiter awaiter, ref TStateMachine stateMachine)`
- `void AsyncTaskMethodBuilder::SetException(Exception exception)`
- `void AsyncTaskMethodBuilder::SetResult()`
- `void AsyncTaskMethodBuilder<T>::Start<TStateMachine>(ref TStateMachine stateMachine)`
- `void AsyncTaskMethodBuilder<T>::AwaitUnsafeOnCompleted<TAwaiter, TStateMachine>(ref TAwaiter awaiter, ref TStateMachine stateMachine)`
- `void AsyncTaskMethodBuilder<T>::SetException(Exception exception)`
- `void AsyncTaskMethodBuilder<T>::SetResult(T result)`

**注意**，Unity在release模式下编译的dll中生成的状态机是ValueType类型，导致无法泛型共享，但debug模式下生成的状态机是class类型，可以泛型共享

如果使用il2cpp泛型共享机制加上 `scriptCompilationSettings.options = ScriptCompilationOptions.DevelopmentBuild;` 代码，编译出的状态机是class类型，在热更新代码中能正常共享

> 使用`补充元数据技术`，则对编译方式无限制

自2021.3.x LTS版本起，il2cpp已经完全支持`完全泛型共享（full generic share）`技术，当 Build Settings中 `Il2Cpp Code Generation` 选项为 `faster(smaller) build` 时开启   
优点是可以任意泛型实例化，而且可以节约代码大小  
缺点是极大地伤害了泛型函数的性能。慢几倍到十几倍，甚至比不上纯解释版本。   
这种机制除了想极端减少包体的场合，基本没有实践意义。

# 桥接函数

HybridCLR的interpreter与AOT之间需要双向函数调用  
对于固定的AOT部分，桥接函数集是确定的，后续无论进行任何热更新，都不会需要新的额外桥接函数。因此不用担心热更上线后突然出现桥接函数缺失的问题

直接使用`HybridCLR/Generate/MethodBridge`
生成桥接函数，但该命令依赖`裁剪后的AOT dll`和`热更新dll`，而`裁剪后的AOT dll`依赖于`生成LinkXml`和`生成Il2CppDef`。

# 内存与GC

HybridCLR是CLR级别的实现 无论在AOT还是热更新中，对象大小是完全相同的

加载程序集时，会复制一份dll文件字节数组，并且内存中动态生成元数据。最终占的内存一般是1-5倍程序集大小

运行过程中, 除了assembly加载和函数第一次transfrom时会额外消耗CPU和内存外，运行时消耗的内存跟il2cpp完全相同

# 执行性能

HybridCLR除了数值计算跟lua持平之外，其他方面数据均大幅（数倍到数十倍）优于lua方案

* C++实现
* 使用寄存器指令集
* 直接访问数据栈和执行栈
* 指令静态特例化
  * 如`add_i4、add_i8、add_r4、add_r8`
* 对象成员访问指令实现简单高效
  * `*(int32_t*)(obj + offset) = b;`
* 直接支持引用与指针操作，无需通过间接方法
* 元数据统一，创建对象更高效，内存占用也更小,函数调用方式统一

# 不支持的特性

- 增量式gc
- 热更新脚本中定义extern函数
- 支持Unity Jobs库，但无法利用burst加速。
- 不支持`System.Runtime.InteropServices.Marshal`中 `Marshal.StructureToPtr`之类序列化结构的函数，但普通Marshal函数如`Marshal.PtrToStringAnsi`都是能正常工作的。
- 不支持[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.xxx)]一个推荐的办法是你使用反射收集这些函数，在合适的时机主动调用它们。
- 不支持对解释代码部分进行C#级别调试
  - 暂无调试器

# 最佳实践

- 推荐将启动脚本挂载到启动热更新场景,不需要任何反射操作  
- `RuntimeApi.LoadMetadataForAOTAssembly`热更新dll加载后但还未执行任何何代码前调用
- AOT与热更层交互, 使用持有delegate方式
- 不要使用 `faster(smaller) builds` 选项

# il2cpp bug记录

* 逆变协变泛型接口调用错误
  * 协变逆变调用结果可能不符合预期
* obj.Func() 非虚调用不符合规范
* 当struct中包含class类型对象时，StructLayout的pack不会生效
  * 内存占用变大
* 泛型数组函数未设置token
* throw null 会导致崩溃
* close delegate 的this为null时，抛出的异常不合规范
* 2019 生成的delegate 调用代码，未正确处理open delegate，并且this为ValueType的情形
  * 可能对项目有影响, 考虑复现排查
* mono及il2cpp不支持 instance method的open delegate 上调用 InvokeDyanmic
* 2019 WebGL平台生成的对象成员访问代码未检查空引用

# FAQ

* HybridCLR增加包体约1.3M
* 主工程可使用`.net standard 2.0`, 热更新dll打包必须用`.net 4.x`
* Assembly最多支持255个
  * 需要手动按顺序加载依赖


------

# 特殊目录一览

`Packages` 用于存放`com.focus-creative-games.hybridclr_unity`

将`il2cpp_plus/libil2cpp`目录和`hybridclr/hybridclr`目录合并，制作出最终的支持热更新的`libil2cpp`

环境变量`UNITY_IL2CPP_PATH`自定义`il2cpp`的位置  
注意, 修改的是进程环境变量, 而非全局, 当然也支持自行设置全局

`{project}/HyridCLRData/LocalIl2CppData-{platform}/il2cpp` 从Editor安装目录复制的il2cpp目录, 被重定向使用

`{project}/HybridCLRData/HotUpdateDlls/{platform}`平台编译开关下编译出的热更新dll

`LinkGenerator/link.xml` 热更新dll引用的AOT类型生成默认路径

`{project}/HybridCLRData/AssembliesPostIl2CppStrip/{platform}` 备份裁剪后的AOT dll 

`{project}/HybridCLRData/iOSBuild` 编译iOS版本`libil2cpp.a`所需的脚本

`Library/ScriptAssemblies` 目录下的assembly是BuildTarget为Editor时编译的结果

`{project}/HybridCLRData/iOSBuild` iOS打包使用的`libil2cpp.a`

# 特殊处理一览

微信小游戏需修改微信工具源码，将`BuildSettings`中 `IL2CPP Code Generation` 设置为 `Faster`

iOS打包需使用自行编译的`libil2cpp.a`

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
<!-- 链接 -->


<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
