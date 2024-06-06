---
title: UnityShader变体优化和故障排除
date: 2024-05-31 22:47:00
updated: 2024-05-31 22:47:00
id: ml-20240531-224700-g199
categories:
	- Unity
	- TA
tags: 
	- Unity
	- TA
	- Shader
---


# 变体是什么

在 Unity 中编写着色器时, 经常会包含多种功能,通道,分支  
在生成时，将包含一个或多个变体  
变体是该着色器的一个版本,遵循一组条件使线性执行路径中没有其他静态分支条件


<!--more-->

原始文章来自 [UnityBlog][UnityShader变体优化和故障排除]

# 为什么使用变体

使用变体是因为 GPU 擅长并行化可预测且始终遵循相同路径的代码，从而产生更高的吞吐量

# 变体的缺陷

但它也有一些缺点。随着变体数量的增加，构建时间会变长，有时每次构建甚至会延长数小时

> 而且生成变体时一个不慎, 便反而性能堪忧(更长启动事件, 更大内存), 或无法渲染(无法找到正确的着色器变体)

> 着色器的初始变体空间达到数百万并不罕见, 通常称为着色器变体爆炸(生成的变体数量无法控制)


# Unity做了什么

Unity 将尝试减少基于一些过滤过程生成的变体数量

例如，如果未启用 XR，则通常会剥离所需的变体  
考虑您在场景中实际使用的功能，例如照明模式、雾等


> 这些特别难以发现，因为开发人员和艺术家可能会引入看似安全的更改，实际上会导致着色器变体的显着增加，**除非您在部署管道中采取一些保护措施，否则没有任何明显的检测方法**

# 开发者需要做什么

我们可以做很多事情来剥离尽可能多的变体，而不会影响游戏的视觉质量  

> 当然这需要相当的工时

## 了解关键字对变体的影响

变体生成通过`shader_feature`和`multi_compile`关键字(`KeyWords`)所有可能的组合

`multi_compile` 始终包含在构建中  
`shader_feature` 包含在材质引用中(没有使用的材质则自动被Unity剔除)

推荐使用`shader_feature`

> 可以点击着色器文件, 来查看使用的KeyWords


通过在同一指令中定义互斥关键字, 避免生成不会启用的变体

```HLSL
#pragma shader_feature LIGHT_LOW_Q LIGHT_HIGH_Q
```


使用指令 `#pragma skip_variants` 来定义排除关键字。在构建时，将跳过包含其中一个关键字的所有变体


## 检查生成的着色器代码

点击着色器, 在其选项中找到`Compiled code`用来检查特定构建平台或图形 API 的结果着色器变体


此外，将生成的代码粘贴到 性能分析工具（如 PVRShaderEditor）中，以便进一步优化

最下方还显示根据当前打开的场景中存在的材质，包含了多少个变体  

> 注意, 这里没有使用任何剥离  
> 点击`Show`将查看其中具体的调试信息

# 确定在构建时生成哪些变体


使用 `IPreprocessShaders` 回调, 进行一次完整打包后, 可以知道编译了那些变体到包内

> 此接口也可以用于剥离不需要的变体

~~Unity有一个`始终包含的着色器`列表, 最好仅在绝对必要时才使用它，因为它很容易导致生成大量变体~~ 不推荐使用

`IPreprocessShaders`内会看到一些相同的变体, 这些变体仍会对构建时间、加载时间和运行时内存使用产生负面影响. 建议手动剥离

成功构建后，可以查看 `Editor.log` 文件, 其`Compiling shader`如下

```
Compiling shader "GameShaders/MyShader" pass "Pass 1" (vp)
	Full variant space:     	608
	After settings filtering:   608
	After built-in stripping:   528
	After scriptable stripping: 528
	Processed in 0.00 seconds
	starting compilation...
	finished in 0.02 seconds. Local cache hits 528 (0.16s CPU time), remote cache hits 0 (0.00s CPU time), compiled 0 variants (0.00s CPU time), skipped 0 variants

Serialized binary data for shader GameShaders/MyShader in 0.00s
	gles3 (total internal programs: 290, unique: 193)
	vulkan (total internal programs: 290, unique: 193)

Compressed shader 'GameShaders/MyShader' on vulkan from 1.35MB to 0.19MB
```

> URP可以设置`Shader Variant Log Level`来过滤不需要的日志  
> 在 Unity 2022.2 或更高版本上, `Export Shader Variants`可以生成Json文件

# 确定在运行时使用哪些变体

`Log Shader Compilation`生成运行时日志

```
Compiled Shader: Folder/ShaderName, pass: PASS_NAME, stage: STAGE_NAME, keywords ACTIVE_KEYWORD_1 ACTIVE_KEYWORD_2
```

> 卸载并重新安装游戏

使用 `Memory Profiler` 在游戏运行时拍摄游戏的快照也可


# 基于图形设置的剥离

`Shader Stripping`大项里面很多的小项用于指定光照, 雾效对于着色器的影响

`Custom`实际上是根据场景推算, 等同于`Import From Current Scene`

> URP可以直接在` Pipeline Settings `中定义

# 基于图形层的剥离

>  仅在使用内置渲染管线时

确保将所有图形层设置为完全相同的设置，以便 Unity 跳过这些变体

> 不然就自己做优化, 这里设置不一样很容易翻几倍变体数量

# 基于图形 API 的剥离

`Other Settings`中`Rendering`项可以设置`Graphics APIs`  

如果是自动, Unity尽可能包含多的图形API支持, 手动则对变体数量有益  
手动的缺点是，可能会限制支持游戏的设备数量(仅当明确你的支持设备时使用)

# 严格的着色器变体匹配

Unity 2022.3 中`Strict Shader variant Matching` 确保 Unity 仅加载关键字组合完全匹配的变体

> 不勾选或更低版本, 实际上会使用其他相似变体, 导致难以发现(调试)问题

# 将使用的变体导出到着色器变体集合中

Unity自带一个编辑器运行时捕获变体的机制  
在`Seeting/Graphics`中, 可以通过`Save to asset`来保存Unity捕获的`.ShaderVariant`文件

确保事先点击`Clear`以获得更准确的样本, 然后尽可能多的体验游戏

> 编写工具手动处理变体也是一个好选择(AssetBundle则~~必须~~强烈建议手动处理)

根据我的经验，它也很**容易出错**。很难确保您在一次游戏中遇到所有必需的变体. **请主要将其用于调试和调查目的**

# 可编写脚本的着色器变体剥离

即使用` IPreprocessShaders.OnProcessShader`或`IPreprocessComputeShaders.OnProcessComputeShader` 

手动移除不必要的变体

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->



<!-- 链接 -->

[UnityShader变体优化和故障排除]:https://blog.unity.com/engine-platform/shader-variants-optimization-troubleshooting-tips "UnityBlog"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
