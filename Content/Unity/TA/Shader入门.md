---
title: Shader入门
date: 2021-11-22 23:23:00
updated: 2021-11-22 23:23:00
id: ml-20211122-232300-g171
categories:
	- Unity
	- TA
tags: 
	- Unity
	- TA
	- Shader
---

[Unity_Documentation_Shader][Unity_Documentation_Shader_Link]

<!--more-->

# 简介

在本文档中，该术语的用法如下：

* `shader or shader program` - 在 GPU 上运行的程序。除非另有说明，否则这意味着着色器程序是图形管线的一部分。
* `Shader object` - Shader 类的一个实例。Shader 对象是着色器程序和其他信息的封装器。
* `ShaderLab` - 一种用于编写着色器的 Unity 特定语言。
* `Shader Graph` - 一种无需编写代码即可创建着色器的工具。
着色器资源 - Unity 项目中扩展名为 .shader 的文件。它定义一个 Shader 对象。
* `Shader Graph asset` - Unity 项目中的文件。它定义一个 Shader 对象。

# 创建

* 可以编写代码来创建一个着色器资源，这是一个带有 .shader 扩展名的文本文件。
* 可以使用 Shader Graph，创建一个 Shader Graph 资源。

> `Project View` 上下文菜单中的 `Assets > Create > Shader`

# 渲染期间的操作顺序

在 Unity 使用 Shader 对象之前

1. Unity creates a list of SubShaders for the Shader object. It adds all of the SubShaders defined in the Shader object, and then adds all of the SubShaders in any fallback Shader objects, in order

当 Unity 首次使用 Shader 对象渲染几何体时，或者当着色器 LOD 值或活动渲染管道更改时

1. Unity iterates over the list of all SubShaders and examines them to determine if they are: compatible with the device hardware; at or below the current shader LOD
 value; and compatible with the active render pipeline
2. If the list contains one or more SubShaders that meet these requirements, it selects the first one. This is the active SubShader
3. If the list does not contain any SubShaders that meet all of the requirements
   1. If the list contains one or more SubShaders that meet the hardware requirements (but do not meet the LOD or render pipeline requirements), Unity selects the first one. This is the active SubShader
   2. If the list contains one or more SubShaders that meet the hardware requirements (but do not meet the LOD or render pipeline requirements), Unity selects the first one. This is the active SubShader

Unity 可以识别使用相同着色器变体的几何体并将其组织成批次实现更高效的渲染。  
每帧一次，对于每批几何体

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[Unity_Documentation_Shader_Link]:https://docs.unity.cn/cn/current/Manual/shader-introduction.html "UnityShader文档"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
