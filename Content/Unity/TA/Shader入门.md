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
[Microsoft_Documentation_HLSL][Microsoft_Documentation_HLSL_Link]

<!--more-->

# 简介

Unity中使用 ShaderLab, HLSL

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


# HLSL 编程指南

数据作为基元流进入图形管道

## 变量

与 C 类似，变量有一些命名限制，具有依赖于它们声明位置的范围属性，并可以附加用户元数据  
与 C 不同，HLSL 定义了其他数据类型，有助于最大程度地提高使用矩阵数学对三维图形数据进行操作的4个分量矢量的性能

使用以下语法规则声明 HLSL 变量

```
[存储 _类 ] [ 类型 _ 修饰符 ] 类型名称 [ 索引 ] [ ：语义 ] [ ： Packoffset ] [ ： Register ] ; [ ] 批注 [= 初始 _值 ]
```

示例
```HLSL
float4 color;
float fVar = 3.1f;

int iVar[3];

int iVar[3] = {1,2,3};

uniform float4 position : SV_POSITION; 
const float4 lightDirection = {0,0,1};
```

# 数据类型

* bool 值-true 或 false。
* int -32 位有符号整数。
* uint -32 位无符号整数。
* dword -32 位无符号整数。
* half  16 位浮点值。 此数据类型仅用于语言兼容性。 Direct3D 10 着色器目标将所有半个数据类型映射为 float 数据类型。 在统一全局变量上不能使用半种数据类型 (如果需要此功能) 使用/Gec 标志。
* float -32 位浮点值。
* double  64 位浮点值。 不能使用双精度值作为流的输入和输出。 若要在着色器之间传递双精度值，请将每个 双 精度值声明为一对 uint 数据类型。 然后，使用 asuint 函数将每个 double 打包到一对 uint s，并使用 asdouble 函数将一对 uint s 重新打包回 double。

从 Windows 8 HLSL 开始也支持最小精度标量数据类型  
图形驱动程序可能会以完整的32位精度对 min16float 值执行算术运算,这取决于是否支持最小精度

min16float -最小16位浮点值。
min10float -最小10位浮点值。
min16int -最小16位有符号整数。
min12int -最小12位有符号整数。
min16uint -最小16位无符号整数。

# 矢量类型

向量包含一到四个标量组件;向量的每一个分量都必须是同一类型

```
float3  fVector = { 0.2f, 0.3f, 0.4f };
```

也可使用此语法声明向量
```
vector <double, 4> dVector = { 0.2, 0.3, 0.4, 0.5 };
```

矢量最多包含四个组件，每个组件都可以使用以下两个命名集之一进行访问

* 位置集： x、y、z 和 w
* 颜色集： r、g、b、a

```
float4 pos = float4(0,0,2,1);
float2 f_2D;
f_2D = pos.xy;   // read two components 
f_2D = pos.xz;   // read components in any order       
f_2D = pos.zx;

f_2D = pos.xx;   // components can be read more than once
f_2D = pos.yy;

float4 f_4D;
f_4D    = pos;     // write four components   
f_4D.xzyw = pos.w; // write one component to more than one component
f_4D.wzyx = pos;


//f_4D.xx = pos.xy;   // 不能将分配多次写入同一组件。 因此，此语句的左侧无效
//f_4D.xg = pos.rgrg;    // 而且，组件名称空间不能混合。 这是无效的组件写入

//下面两个语句是等效的
f_4D.a = pos * 5.0f;
f_4D.a = pos.r * 5.0f;
```

# 矩阵类型

矩阵是一种特殊数据类型，包含一到十六个组件。 矩阵的每一个分量都必须具有相同的类型

```
float2x2 fMatrix = { 0.0f, 0.1, // row 1
                     2.1f, 2.2f // row 2
                   };   
```

也可使用此语法声明矩阵

```
matrix <float, 2, 2> fMatrix = { 0.0f, 0.1, // row 1
                                 2.1f, 2.2f // row 2
                               };
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[Unity_Documentation_Shader_Link]:https://docs.unity.cn/cn/current/Manual/shader-introduction.html "UnityShader文档"

[Microsoft_Documentation_HLSL_Link]:https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl "高级着色器语言 (HLSL)"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
