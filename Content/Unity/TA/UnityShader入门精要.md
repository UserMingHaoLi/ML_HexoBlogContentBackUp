---
title: Shader入门
date: 2021-12-05 22:46:00
updated: 2021-12-05 22:46:00
id: ml-20211205-224600-g174
categories:
	- Unity
	- TA
tags: 
	- Unity
	- TA
	- Shader
---

本书GitHub地址(https://github.com/candycat1992/Unity_Shaders_Book/tree/master)

- [渲染流水线](#渲染流水线)
	- [什么是流水线](#什么是流水线)
	- [什么是渲染流水线](#什么是渲染流水线)
		- [应用阶段](#应用阶段)
		- [几何阶段](#几何阶段)
		- [光栅化阶段](#光栅化阶段)
	- [CPU和GPU之间的通信](#cpu和gpu之间的通信)
		- [吧数据加载到显存中](#吧数据加载到显存中)
		- [设置渲染状态](#设置渲染状态)
		- [调用Draw Call](#调用draw-call)
		- [概述](#概述)
	- [GPU流水线](#gpu流水线)
		- [顶点着色器](#顶点着色器)
		- [裁剪](#裁剪)
		- [屏幕映射(Screen Mapping)](#屏幕映射screen-mapping)
		- [三角形设置](#三角形设置)
		- [三角形遍历(Triangle Traversal)](#三角形遍历triangle-traversal)
		- [片元着色器](#片元着色器)
		- [逐片元操作](#逐片元操作)
	- [总结](#总结)
- [Unity Shader基础](#unity-shader基础)
	- [概述](#概述-1)
	- [UnityShader的基础:ShaderLab](#unityshader的基础shaderlab)
	- [UnityShader的结构](#unityshader的结构)
	- [UnityShader的形式](#unityshader的形式)
		- [表面着色器](#表面着色器)
		- [顶点, 片元着色器](#顶点-片元着色器)
		- [固定函数着色器](#固定函数着色器)
- [学习Shader所需的数学基础](#学习shader所需的数学基础)
	- [笛卡尔坐标系](#笛卡尔坐标系)
		- [左手坐标系和右手坐标系](#左手坐标系和右手坐标系)
		- [习题](#习题)
	- [点和矢量](#点和矢量)
		- [矢量和标量的乘除法](#矢量和标量的乘除法)
		- [矢量的加减](#矢量的加减)
		- [矢量的模](#矢量的模)
		- [矢量的点积](#矢量的点积)
		- [矢量的叉积](#矢量的叉积)
		- [习题](#习题-1)
	- [矩阵](#矩阵)
		- [矩阵与标量相乘](#矩阵与标量相乘)
		- [矩阵相乘](#矩阵相乘)
		- [定义](#定义)
		- [习题](#习题-2)
		- [矩阵的几何意义:变换](#矩阵的几何意义变换)
	- [答疑](#答疑)
		- [使用33矩阵还是44矩阵](#使用33矩阵还是44矩阵)
		- [CG中的矢量和矩阵](#cg中的矢量和矩阵)
		- [Unity中的屏幕坐标](#unity中的屏幕坐标)
- [开始UnityShader学习之旅](#开始unityshader学习之旅)
	- [Debug](#debug)
	- [注意跨平台渲染的差异](#注意跨平台渲染的差异)
- [Unity中的基础光照](#unity中的基础光照)
	- [标准光照模型](#标准光照模型)
		- [总结](#总结-1)
	- [实践:逐顶点光照](#实践逐顶点光照)
	- [实践:逐像素光照](#实践逐像素光照)
- [基础纹理](#基础纹理)
	- [法线纹理](#法线纹理)
	- [渐变纹理](#渐变纹理)
- [遮罩纹理](#遮罩纹理)
- [透明效果](#透明效果)
	- [透明度测试](#透明度测试)
	- [透明度混合](#透明度混合)
	- [开启深度写入的半透明效果](#开启深度写入的半透明效果)
	- [ShaderLab的混合命令](#shaderlab的混合命令)
	- [双面透明的渲染效果](#双面透明的渲染效果)
- [更复杂的光照](#更复杂的光照)
	- [Unity的阴影](#unity的阴影)
	- [透明物体的阴影](#透明物体的阴影)
	- [标准Shader](#标准shader)
- [高级纹理](#高级纹理)
	- [立方体纹理](#立方体纹理)
		- [天空盒子](#天空盒子)
		- [环境映射的立方体纹理](#环境映射的立方体纹理)
		- [反射](#反射)
		- [折射](#折射)
	- [渲染纹理](#渲染纹理)
		- [镜子效果](#镜子效果)
		- [玻璃效果](#玻璃效果)
		- [渲染纹理 vs.GrabPass](#渲染纹理-vsgrabpass)
	- [程序纹理](#程序纹理)
		- [Unity中的程序纹理](#unity中的程序纹理)
		- [Unity的程序材质](#unity的程序材质)
- [完毕](#完毕)

<!--more-->

# 渲染流水线

渲染流水线的最终目的在于生成(渲染)一张二维纹理, 即我们在电脑屏幕上看到的所有效果, 他的输入是一个虚拟摄像机, 一些光源, 一些Shader以及纹理等.

## 什么是流水线

![2_1-真实生活中的流水线][2_1-真实生活中的流水线]

当一个产品需要四个步骤,每个步骤需要一小时, 并不是说生产一个产品就需要四小时, 通过**流水线**每个步骤同时进行,就可以一小时产生一个产品.  
可用发现,流水线系统的运行效率取决于其中最慢的工序.  
理想情况下,将一个非流水线系统分为n个流水线阶段, 且每个阶段耗时相同, 则整个系统会获得n倍的效率提升.

## 什么是渲染流水线

将上述流水线的概念普及到计算机的图形渲染种  
渲染流水线的工作任务在于,由一个三维场景出发, 生成一张二维图像, 计算机需要从一系列参数(顶点,纹理,等)信息出发, 把这些信息转换为人眼可以看到的图像, 这些工作由CPU和GPU共同完成

![流水线中的三个概念阶段][流水线中的三个概念阶段]

`<Render-Time Rendering, Third Edition>`书种将渲染分为3个阶段
* 应用阶段(Application Stage)
* 几何阶段(Geometry Stage)
* 光栅化阶段(Resterizer Stage)

> 注意,这仅仅是概念性的阶段,每个阶段通常也是一个流水线系统

### 应用阶段

由应用主导,通常由CPU负责实现, 开发者拥有这个阶段的控制权

开发者有三个任务

1. 主备好场景数据,如`摄像机位置`,`视锥体`,`场景中的模型`,`光源`等.
2. 为提高渲染效率,需要进行一些粗粒度剔除工作, 把那些不可见的物体剔除出去,不需要将他们提交给几何阶段
3. 设置每个模型的渲染状态, 这些渲染状态包括但不限于`材质(漫反射,高光反射)`,`纹理`,`使用的Shader`等.

这一阶段最重要的是要输出渲染所需的几何信息,即**渲染图元(rendering primitives)**  
通俗来讲,渲染图元可以是点,线,三角面等, 这些渲染图元会被传递给下个阶段---`几何阶段`

> 剔除一般含有`遮挡剔除`,`层级剔除`,`视锥体剔除`等, 这取决于开发者.  

> 粗粒度剔除, 这是为了区分`裁剪(clipping)`与`剔除(culling)`的区别,  
> 粒度不同是指,*剔除是在object层面的*，对object的各项属性做处理,并决定是否部将其传入几何阶段, *裁剪是在图元（三角形）层面的*,对构成object mesh的三角形再做裁剪

### 几何阶段

处理所有和我们要绘制的几何相关的事情, 例如,决定需要绘制的图元是什么,怎样绘制他们,在哪里绘制他们.这一阶段通常在GPU上进行.

几何阶段负责和每个渲染图元打交道, 进行逐顶点,逐多边形的操作.  
这一阶段还可以细分为更小的流水线阶段,后面会提到.  

这一阶段最重要的是吧**顶点坐标变换到屏幕空间**中, 再交给*光栅器*进行处理.  
通过对输入的渲染图元进行多部处理后, 这一阶段将会输出屏幕空间的二维顶点坐标, 每个顶点对应的深度值,着色等相关信息, 并传递给下个阶段.

### 光栅化阶段

接受上个阶段传递的数据来生成屏幕上的像素, 并渲染出最终的图像, 这一阶段也是在GPU上进行的.

光栅化的任务主要是决定每个渲染图元中的那些像素应该被绘制在屏幕上  
这需要对上个阶段得到的*逐顶点数据(纹理坐标,顶点颜色等)*进行插值, 然后进行逐像素处理.

光栅化也可以分成更小的流水线

> 上述所有流水线均为概念流水线, 是依据渲染流程进行基本功能划分而提出的, 下面介绍GPU的流水线, 则是硬件真正用于实现上述概念的实际流水线.

## CPU和GPU之间的通信

渲染流水线起始于CPU,也就是应用阶段, 大致分为下面3个阶段.
1. 吧数据加载到显存中
2. 设置渲染状态
3. 调用`Draw Call`(后续讨论Draw Call是什么)

### 吧数据加载到显存中

硬盘, 内存, 显存, 访问速度越来越快  

而且, 绝大多数显卡对于内存没有访问权限

理论上讲, 数据加载到显存后,就可以从内存中卸载他们了, 但是CPU可能希望访问这些数据, 所以有时候并不会卸载.

### 设置渲染状态

指使用的顶点着色器, 片元着色器, 光源,材质等数据

### 调用Draw Call

所有数据准备完毕后, CPU就可以通知GPU来进行渲染了.

这就是一个Draw Call  
由CPU发起,GPU执行, 这个指定传递一个图元列表, 这是CPU处理后的数据集合

### 概述

应用阶段(CPU), 开发者拥有很大的主导权, 但到了几何阶段(GPU),光栅化阶段(GPU), 我们能做的事情有限.

## GPU流水线

* 顶点数据
* 几何阶段
  * 顶点着色器(必须, 可编程)
  * 曲面细分着色器(非必须, 可编程)
  * 几何着色器(非必须, 可编程)
  * 裁剪(可配置)
  * 屏幕映射(无权限)
* 光栅化阶段
  * 三角形设置(无权限)
  * 三角形遍历(无权限)
  * 片元着色器(非必须, 可编程)
  * 逐片元操作(可配置)
* 屏幕图像

### 顶点着色器

流水线的第一个阶段.  

每个输入的顶点都会调用一次顶点着色器  
顶点着色器并不创建或者销毁任何顶点, 也无法得知顶点与顶点之间的关系  
但正是因为这样的相互独立性, GPU可以利用本身的特性并行处理每个顶点

这意味着, 这一阶段的处理将会很快.

顶点着色器的主要工作内容有: 坐标变换,逐顶点光照,构建自定义数据.

利用变换坐标功能, 我们可以动态改变顶点坐标, 以达成水面,布料等功能.  
当然, 不要忘了最基础的, **一定要**吧顶点坐标转换到裁剪空间.

```HLSL
o.pos = mul(UNITY_MVP, v.position);
```

接下来就是硬件做透视除法, 得到归一化设备坐标(NDC, Normalized Device Coordinates)

> 后续的曲面细分着色器,几何着色器,自行了解

### 裁剪

裁剪, 区别于之前应用阶段的剔除, 这里是针对顶点和三角面的  
只有在视野内的顶点才会保留, 视野外的顶点被丢弃, 如果一个三角面一半在内一半在外, 则丢弃外部的, 重写生成符合视野的新三角面.

> 此过程无法编程, 但可以进行一些配置.

### 屏幕映射(Screen Mapping)

讲裁剪空间下的xy坐标转换到屏幕坐标系(Screen Coordinates)  
也是依据屏幕大小缩放.

其中z值不会处理, 将和xy共同构成窗口坐标系(Window Coordinates), 这些值会被一起传递到光栅化阶段.

> OpenGL定义左下为(0,0), DirectX定义左上为(0,0)  
> 这可能是由于微软窗口就是这样定义的, 也可能是源于人类阅读方式, 从上到下, 从左到右.

### 三角形设置

现在开始进入光栅化阶段

光栅化阶段的目标是计算图元覆盖了哪些像素, 以及计算这些像素的值.

几何极端进行的都是顶点操作, 现在我们依据顶点形成三角形, 这就是三角形设置

### 三角形遍历(Triangle Traversal)

检查每个像素是否被一个三角形覆盖, 如果是,则生成一个片元(fragment).  
也被称为扫描变换(Scan Conversion)

> 首先依据两个顶点链接成线, 对线中的片元执行依据顶点的插值计算, 之后对三角形的点进行生成和插值计算

> 片元并非真正的像素, 而是包含了很多状态的数据集合, 包括用于计算像素最终值的颜色, 屏幕坐标, 深度, 其他从几何阶段定义的信息(顶点,法线,纹理等.)

### 片元着色器

在DirectX中也被称为像素着色器, 但实际他们还不是像素.

片元着色器接受上一阶段插值生成的这些片元数据, 进行一些可编程操作.

最重要的就是纹理采样, 使用插值后的数据, 采样对应的纹理颜色.

但注意, 每个片元并不能讲自己的信息共享给邻居.

### 逐片元操作

DirectX中称为 输出合并阶段(Output-Merger) Merger一词便是重点.

这一阶段需要做

1. 决定每个片元的可见性, 涉及到深度测试, 模板测试等.
2. 如果有片元通过测试, 讲其颜色与颜色缓冲区中的颜色进行合并, 或者说混合.

这一阶段虽然不能编程, 但是高度可配置的.

> 没有过通过测试的片元, 直接被丢弃, 之前的工作的白费了.  
> 所以有些时候可以看到提前的深度测试, 称为`Early-Z`.  
> 但这有带来其他问题, 提前筛选掉的片元不在参与其他测试, 导致效果不符合预期. 但是现代GPU一般有一个判断, 依据操作是否可能和提前测试冲突来决定是否起开提前测试. 这就是为什么透明度测试会导致性能下降的原因.

最终得到的像素点被存储于后置缓冲区, 一旦后置缓冲区被填满, GPU就将其交换到前置缓冲区, 也就是我们看到的屏幕上的图像. 这保证我们看到的图像总是连续的.

## 总结

每个资料给出的流水线流程和名称各不相同, 这主要是由于编程接口(OpenGL,DirectX)的实现不尽相同, 还有GPU底层的优化导致的.

# Unity Shader基础

Unity提供了一个方便的框架来处理渲染设置和着色器代码.

## 概述

Material和UnityShader, 他们通常是一起出现的, 材质持有UnityShader, 物体持有材质.

UnityShader定义渲染需要的代码(顶点着色器,片元着色器),属性(纹理,反射),指令(渲染设置,标签设置).  
而材质允许我们调节这些属性. 并将其赋值给相应的模型.

> 可以注意到, 上面使用的都是UnityShader, 这是由于UnityShader与之前提及的渲染管线的Shader很大不同, 后面会提到.

## UnityShader的基础:ShaderLab

这是Unity提供给开发者的高层级的渲染抽象层, 使开发者轻松控制渲染.

> Unity会讲ShaderLab编译为真正的Shader代码, 开发者无需关心.  
> 真想关心可以点击`Compile and show code` 查看Unity为不同编程接口生成的底层汇编指令.

## UnityShader的结构

SubShader, 最少有一个, 可以包含多个.  
只会运行其中的一个, 写多个一般是为了适配不同的平台和机型, 以便控制性能和复杂度.

```HLSL
SubShader{
	[Tags]
	[RenderSetup]
	Pass
	{

	}
}
```
Tags是标签, RenderSetup是状态设置, Pass则定义了一个完成的渲染流程, 如果Pass过多, 则会渲染多次, 也自然会消耗更多性能.  
Pass中也可以使用标签和状态设置, 但有些内容是SubShader特有的, 且在SubShader中的设置, 将应用于所有的Pass

```HLSL
Pass
{
	[Name]
	[Tags]
	[RenderSetup]
}
```

拥有Name, 可以用ShaderLab的UsePass命令直接使用其他UnityShader中的Pass  
提高代码的复用性  

Fallback, 可以跟在SubShader语句后面, 用于表示, 如果所有SubShader都不能在此机器上运行, 则使用这个Shader吧.  
基本就是用来表示, 最低级的UnityShader是谁 

当然, 你也可以关闭Fallback, 这表明, 如果不能执行,就不要执行了.

## UnityShader的形式

### 表面着色器

Unity最后还是将其转化为了顶点和片元Shader, 只是帮我们省了一些操作

### 顶点, 片元着色器

### 固定函数着色器

不支持可编程渲染管线, 就可以使用这个.

> 实际已经不存在了, 最终都被编译为了顶点,片元着色器.

> CG和DX9风格的HLSL几乎是同一种语言, 所以我们常说 `CG/HLSL`

# 学习Shader所需的数学基础

## 笛卡尔坐标系

笛卡尔坐标系就是直角坐标系和斜角坐标系的统称

OpenGL的屏幕映射笛卡尔坐标系, 原点在左下  
DiectX的屏幕映射笛卡尔坐标系, 原点在左上

### 左手坐标系和右手坐标系

坐标轴的方向并不是固定的

但在二维的坐标系中, 我们总可以通过旋转,翻转等手段来使坐标系变为相同.  
但三维的坐标系则不能如此

对于一个XY方向相同,仅Z轴颠倒的坐标系, 无论如何旋转,翻转,最多只有两个轴能方向相同.

> 旋转的正方向: 伸出拇指指向要旋转的轴的正方向, 剩下四根手指弯曲的方向就是旋转正方向.

> 左收坐标系可以通过翻转任何一个轴来变为右手坐标系, 再反转就可以转会左手坐标系  
> 一般是吧z轴取反

**对于不同坐标系,达到同样的视觉效果需要做的数学运算不相同**  
反而言之,使用相同的数学运算,会达成不一样的视觉效果

对于模型空间和世界空间, Unity使用左手坐标系   
但对于观察空间(以摄像机为原点的空间), Unity使用右手坐标系

### 习题

1. 右手坐标系
2. 1,0,0 和 1,0,0
3. 0,0,-10, 0,0,10

## 点和矢量

### 矢量和标量的乘除法

也就是标量和矢量的每个位相乘, 或相除  
对于相除, 只能是矢量除以标量, 且标量不能为0

### 矢量的加减

实际上就是矢量的每个对应分量进行加减即可.

从几何意义上来看, a+b等于他们相对于原点的位移, b-a等于b相对于a的位移

### 矢量的模

是一个标量, 可以理解为矢量在空间中的长度

对每个分量进行平方后相加, 然后开方.

> 对于二维矢量, 这其实就是勾股定理

### 矢量的点积

矢量之间的乘法, 点积也被称为内积

> UnityShader中, 可使用`dot(a,b)`来计算点积

点积来自于这种运算符号 `a·b`

有两种形式, 先看第一种  
将两个三维的矢量对应的分量相乘再取和. 结果是一个标量

`(1,2,3)·(0.5,4,2.5) = 0.5 + 8 + 7.5 = 16`

其几何意义为投影, 这几乎存在于图形学的各个方面

有一光源垂直于A方向(视A为地面), B在A方向上的投影就是 B在地面的影子.

注意, 投影可能为负数, 这取决于B相对于A的方向, 也就是两者的夹角  
夹角小于90°则为正数, 等于则为0, 大于则为负数

拥有结合性质, 可与标量相乘, 矢量加减法结合.   
矢量和自身点积, 得到该矢量模的平方.

> 这可以快速比较两个矢量的大小, 也不必开平方, 因为开平方有性能消耗

第二种形式如下

a·b = |a||b|cosθ

cosθ = 直角边/斜边

当|b|等于1时, 投影恰好是直角边

两个矢量的点积,可与表示为两个矢量的模相乘, 再乘以他们之间夹角的余弦值.

> 夹角小于90°则为正数, 等于则为0, 大于则为负数. 这也是余弦的定义

还可以使用`arcos(a·b) = θ`得到夹角(0°~180°)

### 矢量的叉积

矢量之间的乘法, 差积也被称为外积 

得到的结果也是一个矢量

aXb = (Ax,Ay,Az)X(Bx,By,Bz) = (AyBz - AzBy,AzBx - AxBz, AxBy - AyBx)

> 注意: 叉积不满足交换律和结合律

得到的结果是垂直于这两个矢量(构成的面)的新矢量. |aXb| = |a||b|sinθ

注意, 对于一个面, 我们有上下两个方向可以选择.

判断方法, 对于右手坐标系中的叉积, 使用右手法则, 手心位于ab尾部的交点, 手掌方向与a重合, 四个手指向b弯曲, 大拇指的指向就是方向.

### 习题

1. 不是
2. 是
3. 不是
4. 4+49+9=62 约等于 7.874
5. 12.5,10,25
6. 1.5,2
7. 5/13,12/13
8. 1/√3,1/√3,1/√3 约等于 0.577,0.577,0.577
9. 10,9
10. -6,1,2
11. 8,12,10 = √308 约等于 17.55
12. 75
13. 13
14. 6+20=26/2 = 13 
15. -1*1 - 2*4, 2*-5 - 3*1, 3*4-(-1*-5) = -1-8,-10-3,12-(5) = -9,-13,7
16. 4*2 - 1*-1, 1*3 - -5*2, -5*-1-(4*3) = 8-(-1),3-(-10),5-(12) = 9,13,-7
17. 12
18. 12√3 约等于 20.785
19. 点积
20. -2 < 0, 在后方
21. 求得cosθ并于cosф/2比较
22. 取模长判断距离
23. u = p2-p1, v=p3-p1, 取其叉积,也就是三角面的朝向, 其值与观察方向做点积. 正则逆时针(背面), 负则顺时针(正面). 

## 矩阵

向量可以表示为1*n或n*1的矩阵, 这样,向量便可以与矩阵一起运算

### 矩阵与标量相乘

就是矩阵内每个数乘以标量

### 矩阵相乘

一个r*n的矩阵A和一个n*c的矩阵B, 乘出的结果是r*c的矩阵AB

> n必须相等

实际上结果ABij等于A的第i行和b的第j列的向量点积

> 矩阵乘法不满足交换律, 但满足结合律

### 定义

* 方块矩阵
  * 行列相等
* 对角矩阵
  * 只在对角线上有值
* 单位矩阵
  * 任何矩阵与其相乘单位不变
* 转置矩阵
  * 将原先的r*n的行列变为n*r的列行, 元素不变
* 逆矩阵
  * 一个矩阵与它的逆矩阵相乘,得到一个单位矩阵
  * 逆矩阵允许我们还原一个相乘的过程
* 正交矩阵
  * 一个方阵M和他的转置矩阵相乘,得到的结果是单位矩阵
  * 正交矩阵的转置矩阵与逆矩阵相等.

Unity中,矢量被转换为列矩阵

### 习题

1. 存在
```
(1,3)·(-1,0), (1,3)·(5,2)  
(2,4)·(-1,0), (2,4)·(5,2)
等于
-1,11  
-2,18
```
2. 不存在 列和行不等
3. 存在
```
(1,-2,3)·(-5,4,8),
(5,1,4)·(-5,4,8),
(6,0,3)·(-5,4,8),
等于
-5+-8+24 = 11,
-25+4+32 = 11,
-30,0,24 = -6
```
4. 不是
5. 是
6. 是
7. 一样,是单位矩阵
8. 不一样,需要转置
9. 一样,是堆成矩阵,其转置就是自身

### 矩阵的几何意义:变换

变换就是吧数据进行转换的过程

线性变换  
可以保留矢量加和标量乘

平移变换  
平移坐标(三维)

仿射变换  
合并线性变换和平移变换(四维)

> 这里的四维是齐次坐标空间  
> 对于点,w设为1, 对于向量,w设为0, 这将在平移时忽略向量

纯平移,纯旋转,纯缩放的变换矩阵叫做基础变换矩阵

```
M3*3 t3*1
01*3 1
```
这样构成一个4*4的矩阵

平移矩阵  
```
1,0,0,tx
0,1,0,ty
0,0,1,tz
0,0,0,1

x
y
z
1

得出的结果是
x*tx
y*ty
z*tz
1*1
即每个位置位移一个值.

而当右下角为1的时候, 不会产生位移, (tx * 0 = 0)
其逆矩阵就是将所有t变为负数
```

缩放矩阵
```
kx,0,0,0
0,ky,0,0
0,0,kz,0
0,0,0,1

得出的结果是
x*tx
y*ty
z*tz
1*1
即每个位置缩放一个值.

对于矢量也可生效

如果所有k都相等, 那么这是一个统一缩放,不会改变角度和比例信息, 
其逆矩阵是1/k
```

旋转矩阵
```
x轴
1,0,0,0
0,cosθ,-sinθ,0
0,sinθ,cosθ,0
0,0,0,1

y轴
cosθ,0,sinθ,0
0,1,0,0
-sinθ,0,cosθ,0
0,0,0,1

z轴
cosθ,-sinθ,0,0
sinθ,cosθ,0,0
0,0,1,0
0,0,0,1
```

使用这些矩阵, 约定先缩放,在旋转,最后平移

## 答疑

### 使用33矩阵还是44矩阵

对于线性变换(旋转和缩放)使用33即可, 如果存在平移变换,则需要44  
注意,转化为齐次坐标时, 点的w为1, 矢量的w为0

### CG中的矢量和矩阵

对于`mul(a,b)`这样一个矩阵乘法,a为行矩阵,b为列矩阵,也就是说,矩阵的前后位置会影响结果.

CG中的矩阵是按行填充的,即
```
123
456
789
//M[1][0], 取第二行第一列,即4
```

> 但是Unity脚本中提供了一个类型`Matrix4x4`, 这是按列填充的,不要搞混

### Unity中的屏幕坐标

```HLSL
fixed4 frag(float4 sp : VPOS) : SV_Target
{//屏幕坐标除以屏幕分辨率,得到视口空间坐标
	return fixed4(sp.xy / _ScrennParams.xy, 0.0, 1.0);
}
```

对于一个400*300的分辨率, 其x范围`[0.5, 400.5]`, y范围`[0.5, 300.5]`  
这是因为像素中心对应于浮点数0.5

```HLSL
//使用Unity的ComputeScreenPos
o.scrPos = ComputeScreenPos(o.pos);
//齐次除法获得视口坐标
float2 wcoord = i.scrPos.xy / i.scrPos.z
```

# 开始UnityShader学习之旅

略, 基础部分已经写过了

```
//Unity内置的appdata_full
struct appdata_full
{
	float4 vertex : POSITION;
	float4 tangent : TANGENT;
	float4 normal : NORMAL;
	float4 texcoord : TEXCOORD0;
	float4 texcoord1 : TEXCOORD1;
	float4 texcoord2 : TEXCOORD2;
	float4 texcoord3 : TEXCOORD3;
	#if defined(SHADER_API _XBOX460)
	half4 texcoord4 : TEXCOORD4;
	half4 texcoord5 : TEXCOORD5;
	#endif
	fixed4 color : COLOR;
}
```

## Debug

Shader很难调试, 但可以使用 visual studio的`Graphics Debugger`  
还有Unity的`Frame Debugger`

还可以使用`Intel GPA`,`RenderDoc`,`NVIDIA NSight`,`AMD GPU PrefStudio`

可以输出自定义图像颜色来查看数值, 也算是一种Log了...

## 注意跨平台渲染的差异

最经典的就是OpenGL和DirectX

渲染纹理时,由于原点不同,会出现图片翻转的情况, 但是Unity内置帮我们处理过了.

但在抗锯齿情况下,不会被自动翻转

```
#if UNITY_UV_STARTS_AT_TOP
if (_MainTex_TexelSize.y < 0)
	uv.y = 1-uv.y
#endif
```

尽量少的且简单的使用分支和循环语句, GPU和CPU的处理方式并不相同, 花在一个分支的事件, 就可能超过运行所有分支的事件.

不要除以0, 编辑器不会报错, 运行在不同平台效果不同,且是不可预料的

# Unity中的基础光照

要考虑物理现象

* 光线从光源中发射
* 光线与场景中的一些物体交互, 一些光线被物体吸收了,另一些光线被散射到其他方向
* 最后,摄像机吸收了一些光,产生一张图像

光源被认为是一个没有体积的点l.

**辐照度**: 垂直于l的单位面积上单位时间内穿过的能量  
也可以使用光源方向l和表面法线n之间的夹角的余弦值计算  
可以使用l和n的点积计算cosθ

光线由光源发射后, 就会与物体发生散射和吸收

**散射**改变光线的方向,不改变密度和颜色  
**吸收**改变光线的密度和颜色,不改变方向

散射的光线可能散色到物体内部,称为**折射**或**透射**  
也可能散射到物体外部,称为**反射**.

进入内部的光线还会继续与内部的颗粒进行相交, 有些光线最后会重新发射出物体表面, 另一些则被物体吸收, 重新发射的光线具有和入射光不同的方向分布和颜色.

为了区分这两种散射, 光照模型使用高光反射和漫反射来表示  
**高光反射**表示物体表面是如何反射光线的.  
**漫反射**表示有多少光线被折射,吸收,散射出表面

根据入射光线的数量和方向, 可计算处射光线的数量和方向, 通常使用**出射度**来描述它

辐照度和出射度满足线性关系, 他们之间的比值就是材质的漫反射和高光反射属性

> 本章中,假设漫反射部分是没有方向性的,光线在所有方向上是平均分布的. 同时,只考虑某一个特定方向上的高光反射

**着色**指根据材质属性(漫反射等), 光源信息(光源方向,辐照度), 使用一个等式去计算沿某个观察方向的出射度的过程. 此等式称为光照模型. 不同的光照模型有不同的目的, 有些用于描述粗糙的物体表面,有些用于描述金属表面

## 标准光照模型

标准光照模型只关心直接光照, 也就是直接从光源发射,照到物体表面后,经过物体便面一次反射直接进入摄像机的光线

将进入摄像机的光线分为4个部分, 每个部分分别计算其贡献度
* 自发光, 注意,如果没有全局光照,这些自发光并不会真的照亮物体周围,只是它本身看起来更亮
* 高光反射,光线从光源射到模型表面, 完全镜面反射方向辐射多少辐射量
* 漫反射, 该表面会想每个方向散射多少辐射量
* 环境光, 描述其他所有间接光照

漫反射 = (光源颜色·材质漫反射颜色)max(0, n·l)

截取到0是防止物体被从后面来的光线照亮

计算这些光照模型,可以在片元着色器中计算,称为逐像素光照, 也可以在顶点着色器计算,称为逐顶点光照

逐顶点光照拥有较少的计算量, 但只对线性计算插值,所以非线性光照的结果是不正确的, 而且图元内部的颜色总是暗于顶点处的最高颜色值, 可能产生棱角现象

### 总结

标准光照模型是一个经验模型, 并不完全符合现实中的光照现象, 但由于它的易用性,计算速度,得到的效果都比较好, 所以现在仍被广泛使用, 但它也有很多局限性, 首先,很多重要的物理现象, 如菲涅尔反射, 无法表现, 拉丝金属,毛发等,各项异性反射无法表现

## 实践:逐顶点光照

漫反射

```HLSL
Shader "UnityShaderBook/641"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 color : COLOR;
			};

			v2f vert(a2v v)
			{
				v2f o;
				//变换到裁剪空间
				o.pos = UnityObjectToClipPos(v.vertex);
				//获取环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				//将顶点位移到世界坐标系
				fixed3 worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));
				//世界光源位置, 归一化为方向
				fixed3 worldLight = normalize(_WorldSpaceLightPos0.xyz);
				//漫反射 = 入射光线颜色和强度 * 材质漫反射系数 * (max(法线向量·光源方向))
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLight));
				o.color = ambient + diffuse;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				return fixed4(i.color, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

高光反射
```HLSL
Shader "UnityShaderBook/642"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
			};

			v2f vert(a2v v)
			{
				v2f o;
				//变换到裁剪空间
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
				fixed3 color = ambient + diffuse;
				return fixed4(color, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

半兰伯特模型
```HLSL
Shader "UnityShaderBook/643"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
			};

			v2f vert(a2v v)
			{
				v2f o;
				//变换到裁剪空间
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
				//半兰伯特公式 入射光强度和颜色 * 材质漫反射值 * (α*(法线·光源方向)+β)
				//其中αβ常用值为0.5
				fixed3 halfLambert = dot(worldNormal, worldLightDir) * 0.5 + 0.5;
				//这使得光线在背面也能看到明暗变化,这并没有物理依据而是一种视觉加强技术
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * halfLambert;
				fixed3 color = ambient + diffuse;
				return fixed4(color, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

## 实践:逐像素光照

漫反射
```HLSL
Shader "UnityShaderBook/651"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			half _Gloss;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 color : COLOR;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				//环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				//顶点法线转换到世界坐标系
				fixed3 worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));
				//光源方向
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
				//漫反射
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
				//反射方向 = 2*(法线向量·光源方向)*法线向量-光源方向
				//fixed3 reflectDir = normalize(reflect(-worldLightDir, worldNormal));//使用自带函数, 需要光源指向交点处
				fixed3 reflectDir = normalize(dot(worldNormal,worldLightDir) * 2 * worldNormal - worldLightDir);
				//视角方向
				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - mul(unity_ObjectToWorld, v.vertex).xyz);
				//高光反射 = (入射光颜色和强度·高光反射系数)*max(0,视角方向·反射方向)
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(reflectDir, viewDir)), _Gloss);
				o.color = ambient + diffuse + specular;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				return fixed4(i.color, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

高光反射
```HLSL
Shader "UnityShaderBook/652"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			half _Gloss;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				//环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				//顶点法线转换到世界坐标系
				fixed3 worldNormal = normalize(i.worldNormal);
				//光源方向
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
				//漫反射
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
				//反射方向 = 2*(法线向量·光源方向)*法线向量-光源方向
				//fixed3 reflectDir = normalize(reflect(-worldLightDir, worldNormal));//使用自带函数, 需要光源指向交点处
				fixed3 reflectDir = normalize(dot(worldNormal,worldLightDir) * 2 * worldNormal - worldLightDir);
				//视角方向
				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
				//高光反射 = (入射光颜色和强度·高光反射系数)*max(0,视角方向·反射方向)
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(reflectDir, viewDir)), _Gloss);
				float4 color = float4(ambient + diffuse + specular, 1.0);
				return color;
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

Blinn-Phong模型
```HLSL
Shader "UnityShaderBook/653"
{
	Properties
	{
		//漫反射系数
		_Diffuse("Diffuse", Color) = (1.0,1.0,1.0,1.0)
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			half _Gloss;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				//环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				//顶点法线转换到世界坐标系
				fixed3 worldNormal = normalize(i.worldNormal);
				//光源方向
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				//漫反射
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
				//视角方向
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(i.worldPos));
				//其中h=视角方向和光源方向取平均并归一化而来
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				//Blinn-Phong模型 高光反射=(入射光颜色和强度·高光反射系数)*max(0,法线方向·h) 
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(worldNormal, halfDir)), _Gloss);
				float4 color = float4(ambient + diffuse + specular, 1.0);
				return color;
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

> 注意,以上光照计算形式, 只针对平行光, 对于其他的诸如点光源,聚光灯,计算方式是错误的.

实际编写时候, 往往使用Unity内置的一些函数, 帮助计算.

对于Blinn-Phong模型,使用了一些内置函数

# 基础纹理

纹理最初的目的就是使用一张图片来控制模型的外观, 可以将图片"黏"在物体表面

> **纹素**区分于像素

这些纹理于顶点的相对关系(坐标),通常存储于顶点上, 称为UV坐标.

UV通常被归一化为0-1之间,但采样时不一定是在这个区间, 这就有了平铺模式等采样模式,用于决定不在范围内的坐标如何采样.

> OpenGL和DirectX的坐标系同样适用于此, Unity使用的OpenGL坐标系.

纹理定义和使用如下

```HLSL
//定义
_MainTex("MainTex",2D) = "white" {}
//获取
sampler2D _MainTex;
float4 _MainTex_ST; //带_ST是为了获取该纹理的平移和缩放
//运算
o.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
//函数
fixed3 albedo = tex2D(_MainTex, i.uv).rgb * _Color.rgb;
```

## 法线纹理

由于法线方向取值 -1~1, 像素取值0~1, 所以需要映射`(normal+1)/2`  
想在代码中取法线便要反映射`pixel*2-1`

法线可能存在于模型空间或切线空间  
切线空间是由顶点的切线,法线,切线法线的叉积构成的一个空间.

法线空间的有点
* 更简单,更直观
* 可以平滑插值
切线空间的有点
* 自由度高, 可以应用到不同的网格和模型
  * 所以可宠用
* 可进行UV动画
* 可压缩
  * 仅存储两个方向XY,推导出Z

> 书中主要讲解切线空间

切线空间计算

```HLSL
Shader "UnityShaderBook/712"
{
	Properties
	{
		//漫反射系数
		_Color("Color",Color) = (1.0,1.0,1.0,1.0)
		_MainTex("MainTex", 2D) = "white" {}
		_BumpMap("BumpMap", 2D) = "bump" {} //"bump"是Unity中的关键字,表示模型自带法线信息
		_BumpScale("BumpScale", Float) = 1.0
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			float _BumpScale;
			fixed4 _Specular;
			half _Gloss;
		

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;//切线方向, float4是为了存储副切线
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float3 lightDir : TEXCOORD1;
				float3 viewDir : TEXCOORD2;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
				o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;
				//乘w是以副切线确定方向
				float3 binormal = cross(normalize(v.normal),normalize(v.tangent.xyz)) * v.tangent.w;//叉积
				float3x3 rotation = float3x3(v.tangent.xyz, binormal, v.normal);
				//TANGENT_SPACE_ROTATION //Unity提供的宏定义,等同意上面两行代码
				o.lightDir = mul(rotation, ObjSpaceLightDir(v.vertex)).xyz;
				o.viewDir = mul(rotation, ObjSpaceViewDir(v.vertex)).xyz;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 tangentLightDir = normalize(i.lightDir);
				fixed3 tangentViewDir = normalize(i.viewDir);
				fixed4 packedNormal = tex2D(_BumpMap, i.uv.zw);
				fixed3 tangentNoraml;
				//tangentNoraml.xy = (packedNormal.xy * 2 - 1) * _BumpScale;
				//tangentNoraml.z = sqrt(1.0 - saturate(dot(tangentNoraml.xy, tangentNoraml.xy)));
				tangentNoraml = UnpackNormal(packedNormal);//Unity由压缩优化, 自己求值并不一定正确
				tangentNoraml.xy *= _BumpScale;
				tangentNoraml.z = sqrt(1.0 - saturate(dot(tangentNoraml.xy, tangentNoraml.xy)));
				//纹理
				fixed3 albedo = tex2D(_MainTex, i.uv).rgb * _Color.rgb;
				//环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				//漫反射
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(tangentNoraml, tangentLightDir));
				//其中h=视角方向和光源方向取平均并归一化而来
				fixed3 halfDir = normalize(tangentLightDir + tangentViewDir);
				//Blinn-Phong模型 高光反射=(入射光颜色和强度·高光反射系数)*max(0,法线方向·h) 
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(tangentNoraml, halfDir)), _Gloss);
				float4 color = float4(ambient + diffuse + specular, 1.0);
				return color;
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

世界空间计算
```HLSL

Shader "UnityShaderBook/713"
{
	Properties
	{
		//漫反射系数
		_Color("Color",Color) = (1.0,1.0,1.0,1.0)
		_MainTex("MainTex", 2D) = "white" {}
		_BumpMap("BumpMap", 2D) = "bump" {} //"bump"是Unity中的关键字,表示模型自带法线信息
		_BumpScale("BumpScale", Float) = 1.0
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			float _BumpScale;
			fixed4 _Specular;
			half _Gloss;
		

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;//切线方向, float4是为了存储副切线
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float4 TtoW0 : TEXCOORD1;
				float4 TtoW1 : TEXCOORD2;
				float4 TtoW2 : TEXCOORD3;//用于存储切线空间到世界空间的变换矩阵
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
				o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;
				
				float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);
				fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);
				fixed3 worldBinormal = cross(worldNormal, worldTangent) * v.tangent.w;

				//转置矩阵就是逆矩阵
				o.TtoW0 = float4(worldTangent.x, worldBinormal.x, worldNormal.x, worldPos.x);
				o.TtoW1 = float4(worldTangent.y, worldBinormal.y, worldNormal.y, worldPos.y);
				o.TtoW2 = float4(worldTangent.z, worldBinormal.z, worldNormal.z, worldPos.z);
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				float3 worldPos = float3(i.TtoW0.w, i.TtoW1.w, i.TtoW2.w);
				fixed3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));

				fixed4 packedNormal = tex2D(_BumpMap, i.uv.zw);
				//tangentNoraml.xy = (packedNormal.xy * 2 - 1) * _BumpScale;
				//tangentNoraml.z = sqrt(1.0 - saturate(dot(tangentNoraml.xy, tangentNoraml.xy)));
				fixed3 bump = UnpackNormal(packedNormal);//Unity由压缩优化, 自己求值并不一定正确
				bump.xy *= _BumpScale;
				bump.z = sqrt(1.0 - saturate(dot(bump.xy, bump.xy)));
				bump = normalize(half3(dot(i.TtoW0.xyz, bump), dot(i.TtoW1.xyz, bump), dot(i.TtoW2.xyz, bump)));
				//纹理
				fixed3 albedo = tex2D(_MainTex, i.uv).rgb * _Color.rgb;
				//环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				//漫反射
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(bump, lightDir));
				//其中h=视角方向和光源方向取平均并归一化而来
				fixed3 halfDir = normalize(lightDir + viewDir);
				//Blinn-Phong模型 高光反射=(入射光颜色和强度·高光反射系数)*max(0,法线方向·h) 
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(bump, halfDir)), _Gloss);
				float4 color = float4(ambient + diffuse + specular, 1.0);
				return color;
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

## 渐变纹理

最开始,纹理是为了定义一个物体的表面颜色, 后来,人们发现纹理可以用于存储任何表面属性, 比如使用渐变纹理控制漫反射光照的结果

```HLSL

Shader "UnityShaderBook/713"
{
	Properties
	{
		//漫反射系数
		_Color("Color",Color) = (1.0,1.0,1.0,1.0)
		_RampTex("RampTex",2D) = "white" {}
		_Specular("Specular", Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"
			fixed4 _Color;
			sampler2D _RampTex;
			float4 _RampTex_ST;
			fixed4 _Specular;
			half _Gloss;
		

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				float3 worldPos : TEXCOORD2;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				//o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				//o.uv = tex2D(_RampTex, v.texcoord);
				o.uv = TRANSFORM_TEX(v.texcoord, _RampTex);
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed halfLambert = 0.5 * dot(worldNormal, worldLightDir) + 0.5;
				fixed3 diffuseColor = tex2D(_RampTex, fixed2(halfLambert, halfLambert)).rgb * _Color.rgb;

				fixed3 diffuse = _LightColor0.rgb * diffuseColor;

				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(i.worldPos));
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(worldNormal, halfDir)),_Gloss);
				return fixed4(ambient + diffuse + specular, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

# 遮罩纹理

这里通过遮罩纹理保护目标不受光照, 事实上, 可以任意利用其RGBA四个通道, 存储不同的属性, 用于精确控制任何逐像素的表面属性

> DOTA2中, 每个模型使用四张纹理, 颜色,表面法线,两张遮罩(共计8通道)  
> 可前往DOTA2官网学习 [Dota 2 物品创意工坊流程指导主页](https://www.dota2.com/workshop/)

```HLSL
Shader "UnityShaderBook/714"
{
	Properties
	{
		//漫反射系数
		_Color("Color",Color) = (1.0,1.0,1.0,1.0)
		_MainTex("MainTex",2D) = "white" {}
		_BumpMap("BumpMap",2D) = "bump" {}
		_BumpScale("BumpScale",Float) = 1.0
		_SpecularMask("SpecularMask",2D) = "white" {}
		_SpecularScale("SpecularScale",Float) = 1.0
		_Specular("Specular",Color) = (1.0,1.0,1.0,1.0)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Pass
		{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			//float4 _BumpMap_ST;
			float _BumpScale;
			sampler2D _SpecularMask;
			//float4 _SpecularMask_ST;
			float _SpecularScale;
			fixed4 _Specular;
			half _Gloss;
		

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;
				float3 lightDir : TEXCOORD1;
				float3 viewDir : TEXCOORD2;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
				float3 binormal = cross(normalize(v.normal), normalize(v.tangent.xyz)) * v.tangent.w;//叉积
				float3x3 rotation = float3x3(v.tangent.xyz, binormal, v.normal);//切线空间
				//TANGENT_SPACE_ROTATION //Unity提供的宏定义,等同意上面两行代码
				o.lightDir = mul(rotation, ObjSpaceLightDir(v.vertex)).xyz; 
				o.viewDir = mul(rotation, ObjSpaceViewDir(v.vertex)).xyz;
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 tangentLightDir = normalize(i.lightDir);
				fixed3 tangentViewDir = normalize(i.viewDir);
				fixed3 tangentNormal = UnpackNormal(tex2D(_BumpMap, i.uv)); 
				tangentNormal.xy *= _BumpScale;
				tangentNormal.z = sqrt(1.0 - saturate(dot(tangentNormal.xy, tangentNormal.xy)));

				fixed3 albedo = tex2D(_MainTex, i.uv).rgb * _Color.rgb;
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;

				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(tangentNormal, tangentLightDir));

				fixed3 halfDir = normalize(tangentLightDir + tangentViewDir);

				fixed specularMask = tex2D(_SpecularMask, i.uv).r * _SpecularScale;
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(tangentNormal, halfDir)),_Gloss) * specularMask;
				return fixed4(ambient + diffuse + specular, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

# 透明效果

半透明渲染需要先渲染不透明的,再(离摄像机)由远到近渲染.才能得到正确的颜色.

但是远近是基于物体的, 深度值是基于像素(片元)的,对于多物体交叉叠加,或是遮挡重合的情况, 显示出的结果总是错误的.  
为减少错误的情况,尽可能让模型总是凸面体, 并考虑将复杂的模型拆分成可独立排序的多个子模型.  
也可以让透明通道变得柔和,使得错误不是很明显

Unity使用`Queue`来表示渲染队列, 值越小越先被渲染.

Unity预定义的队列
* Background 1000 背景
* Geometry 2000 默认队列
* AlphaTest 2450 需要透明度测试
* Transparent 3000 透明队列, 处于这之后的将从后往前排序渲染.
* Overlay 最后渲染,可实现叠加效果

这些定义再SubShader的Tags中`Tags {"Queue"="Transparent"}`, 并在Pass中关闭深度写入`Zwrite Off`

## 透明度测试

一般使用CG中的一个函数`void Clip(float x)`, 他有多个重载用于接受各种参数

如果给定的参数是负数,则舍弃当前像素(片元)的输出颜色

```HLSL

Shader "UnityShaderBook/831"
{
	Properties
	{
		//漫反射系数
			_Color("Color",Color) = (1.0,1.0,1.0,1.0)
			_MainTex("MainTex",2D) = "white" {}
			_Cutoff("Cutoff",Range(-0.1,2.0)) = 0.5
	}
		SubShader
			{
			//透明度测试需要的Tags
			Tags {"Queue" = "AlphaTest" "Ignoreprojector" = "True" "RenderType" = "TransparentCutout"}
			Pass
			{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _Cutoff;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				float3 worldPos : TEXCOORD2;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				fixed4 texColor = tex2D(_MainTex, i.uv);

				clip(texColor.a - _Cutoff);
				//if(texColor.a - _Cutoff > 0.0) discard;

				fixed3 albedo = texColor.rgb * _Color.rgb;
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormal, worldLightDir));

				return fixed4(ambient + diffuse, 1.0);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```

## 透明度混合

使用`Bland`来控制混合模式

自定义混合公式(AlphaBlend = SrcColor片元颜色 * **SrcAlpha片元Alpha** + DestColor缓冲区颜色 * **(1.0-SrcAlpha)**)

* Blend Off 关闭混合
* Blend SrcFactor DstFactor 开启混合,并设置RGBA混合因子
* Blend SrcFactor DstFactor SrcFactorA DstFactoryA 开启,RGB混合因子,A混合因子
* BlendOp BlendOperation 并非是简单的加后混合,有其他操作

```HLSL

Shader "UnityShaderBook/841"
{
	Properties
	{
		//漫反射系数
			_Color("Color",Color) = (1.0,1.0,1.0,1.0)
			_MainTex("MainTex",2D) = "white" {}
			_AlphaScale("AlphaScale",Range(-0.1,2.0)) = 1
	}
		SubShader
			{
			//透明度测试需要的Tags
			Tags {"Queue" = "AlphaTest" "Ignoreprojector" = "True" "RenderType" = "TransparentCutout"}
			Pass
			{
			//定义正确的LightMode,才能获取一些Unity内置光照变量
			Tags {"LightMode" = "ForwardBase" }
			ZWRite Off
			Blend SrcAlpha OneMinusSrcAlpha
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "Lighting.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _AlphaScale;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				float3 worldPos : TEXCOORD2;
			};

			v2f vert(a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				return o;
			}

			float4 frag(v2f i) : SV_Target
			{
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				fixed4 texColor = tex2D(_MainTex, i.uv);

				fixed3 albedo = texColor.rgb * _Color.rgb;
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormal, worldLightDir));

				return fixed4(ambient + diffuse, texColor.a * _AlphaScale);
			}

			ENDCG
		}
	}
	//Fallback "Diffuse"
}
```
仅改变了返回颜色的透明度

## 开启深度写入的半透明效果

使用两个Pass来渲染, 第一个写入深度, 第二个进行透明渲染, 这样得到的模型不会暴露其内部和身后的像素.

仅需将此Pass新增此处
```HLSL
SubShader
{
	//透明度测试需要的Tags
	Tags {"Queue" = "AlphaTest" "Ignoreprojector" = "True" "RenderType" = "TransparentCutout"}
	Pass
	{
		ZWrite On
		ColorMask 0
	}
	Pass
	{
	//定义正确的LightMode,才能获取一些Unity内置光照变量
	Tags {"LightMode" = "ForwardBase" }
	//...
	}
}
```

## ShaderLab的混合命令

就是这一段

```
Blend SrcAlpha OneMinusSrcAlpha
```

他是高度可定制的, 因子分为两个,四个,区别只是是否区分Alpha通道.

参数如下

* One
* Zero
* SrcColor
* SrcAlpha
* DstColor
* DstAlpha
* OneMinusSrcColor
* OneMinusSrcAlpha
* OneMinusDstColor
* OneMinusDstAlpha

对于 自定义混合公式(AlphaBlend = SrcColor片元颜色 * **SrcAlpha片元Alpha** + DestColor缓冲区颜色 * **(1.0-SrcAlpha)**)

这个加法也可以自定义

使用`BlendOp BlendOperation`

* Add
* Sub
* RevSub
* Min
* Max

常见的混合类型
```HLSL
//正常透明度混合
Blend SrcAlpha OneMinusSrcAlpha
//柔和叠加
Blend OneMinusDstColor One
//正片叠底
Blend DstColor Zero
//两倍相乘
Blend DstColor SrcColor
//变暗
BlendOp Min
Blend One One
//变亮
BlendOp Max
Blend One One
//滤色
Blend OneMinusDstColor One
//等同于
Blend One OneMinusSrcColor
//线性减淡
Blend One One
```

## 双面透明的渲染效果

`Cull Back` 默认是这个,会剔除背面的图元.  
设为 `Off` 则会导致渲染面数成倍增加

只需在Shader831的Pass中添加一行代码, 即可透过透明部分看到背面
```HLSL
Cull Off
```
但这只是透明度测试的双面渲染, 透明度混合的双面渲染要困难很多  
因为要保证图元是从后往前渲染的.  
直接关闭,就无法保证背面和正面的渲染顺序,导致颜色错误

可以分成两个Pass,一个渲染背面,一个渲染正面,当然这是有性能开销的.

在Shader841的基础上,复制一个原本的Pass,使其有两个Pass  
其中第一个`Cull Front`, 第二个`Cull Back`
```HLSL
Pass
{
	Cull Front
	//...
}
Pass
{
	Cull Back
	//...
}
```

# 更复杂的光照

`LightMode=ForwardBase`指定使用什么样的光源信息, 这指示Unity可以按一定格式准备光照信息.

前向渲染, 逐像素光源, N*M个Pass

延迟渲染, 2Pass,第一个不进行光照,只计算可见性并存储可见, 第二个计算可见片元的光照信息.  
适合光源较多的情况下使用,提升性能  
* 不支持真正的抗锯齿功能
* 不能处理半透明物体
* 对显卡有一定要求

默认缓存G存储漫反射,高光反射-A为指数,法线,(自发光,LightMap,反射探长)

---

前向渲染处理不同光源类型
```HLSL
Shader "UnityShaderBook/8712"
	Properties
	{
		_Diffuse("Diffuse", Color) = (1, 1, 1, 1)
		_Specular("Specular", Color) = (1, 1, 1, 1)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader{
		Tags { "RenderType" = "Opaque" }

		Pass 
		{
			Tags { "LightMode" = "ForwardBase" }

			CGPROGRAM

			// 获取光照数据必须
			#pragma multi_compile_fwdbase	

			#pragma vertex vert
			#pragma fragment frag

			#include "Lighting.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			float _Gloss;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
			};

			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);

				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * max(0, dot(worldNormal, worldLightDir));

				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);

				fixed atten = 1.0;//衰减

				return fixed4(ambient + (diffuse + specular) * atten, 1.0);
			}

			ENDCG
		}

		Pass 
		{
			Tags { "LightMode" = "ForwardAdd" } //逐像素光源

			Blend One One //不混合就会覆盖,所以要混合

			CGPROGRAM

			#pragma multi_compile_fwdadd

			#pragma vertex vert
			#pragma fragment frag

			#include "Lighting.cginc"
			#include "AutoLight.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			float _Gloss;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
			};

			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				#ifdef USING_DIRECTIONAL_LIGHT //平行光就有这个宏
					fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);//平行光只要方向
				#else
					fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz - i.worldPos.xyz);//其他光还要通过位置取方向
				#endif

				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * max(0, dot(worldNormal, worldLightDir));

				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);

				#ifdef USING_DIRECTIONAL_LIGHT //衰减
					fixed atten = 1.0;
				#else
					#if defined (POINT)
						float3 lightCoord = mul(unity_WorldToLight, float4(i.worldPos, 1)).xyz;
						fixed atten = tex2D(_LightTexture0, dot(lightCoord, lightCoord).rr).UNITY_ATTEN_CHANNEL; //Unity准备了纹理表方便取值
					#elif defined (SPOT)
						float4 lightCoord = mul(unity_WorldToLight, float4(i.worldPos, 1));
						fixed atten = (lightCoord.z > 0) * tex2D(_LightTexture0, lightCoord.xy / lightCoord.w + 0.5).w * tex2D(_LightTextureB0, dot(lightCoord, lightCoord).rr).UNITY_ATTEN_CHANNEL;
					#else
						fixed atten = 1.0;
					#endif
				#endif

				return fixed4((diffuse + specular) * atten, 1.0);
			}

			ENDCG
		}
	}
	FallBack "Specular"
}
```

unity会自动判断哪个光源重要,取前四个渲染, 也可以手动设置重要,无影响.

Unity准备了纹理表方便取值
* 需要预处理得到纹理
* 不直观,难以二次计算
* 如果有Cookie需要使用_LightTextureB0
* 0,0表示光源,1,1表示光源所关心的最远衰减

手动计算线性衰减
```HLSL
float distance = length(_WorldSpaceLightPos0.xyz - i.worldPosition.xyz);
atten = 1.0 / dostance;
```

## Unity的阴影

常用ShadowMap技术, 将摄像机与光源重合, 摄像机看不到的地方就是阴影所在地.

本质上,Unity采用了预处理的深度图.即摄像机所看到物体的深度信息

使用`LightMode = SHadowCaster`处理阴影图深度Pass, 用于得到阴影映射纹理(深度纹理)  
如果没有此Pass,则查找FallBack, 仍然没有,则不投射阴影,但仍然接受阴影  
阴影图是屏幕空间下的, 所以采样需要变换.

* 如果想要接受阴影, 需要在Shader中对阴影映射纹理采样, 把结果计算成阴影效果
* 如果想投射阴影, 就必须加入`LightMode = SHadowCaster`的Pass
  * 需要开启物体身上MeshRender的CastShadows

使用上一节的代码,不做任何修改就有阴影了, 这是由于Fallback中就有我么你需要的Pass

实现接受阴影
```HLSL

Shader "UnityShaderBook/942"
{
	Properties
	{
		_Diffuse("Diffuse", Color) = (1, 1, 1, 1)
		_Specular("Specular", Color) = (1, 1, 1, 1)
		_Gloss("Gloss", Range(8.0, 256)) = 20
	}
	SubShader
	{
		Tags { "RenderType" = "Opaque" }

		Pass 
		{
			// Pass for ambient light & first pixel light (directional light)
			Tags { "LightMode" = "ForwardBase" }

			CGPROGRAM

			// Apparently need to add this declaration 
			#pragma multi_compile_fwdbase	

			#pragma vertex vert
			#pragma fragment frag

			// Need these files to get built-in macros
			#include "Lighting.cginc"
			#include "AutoLight.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			float _Gloss;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
				SHADOW_COORDS(2)
			};

			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				// Pass shadow coordinates to pixel shader
				TRANSFER_SHADOW(o);

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);

				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * max(0, dot(worldNormal, worldLightDir));

				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);

				fixed atten = 1.0;

				fixed shadow = SHADOW_ATTENUATION(i);

				return fixed4(ambient + (diffuse + specular) * atten * shadow, 1.0);
			}

			ENDCG
		}

		Pass 
		{
			// Pass for other pixel lights
			Tags { "LightMode" = "ForwardAdd" }

			Blend One One

			CGPROGRAM

			// Apparently need to add this declaration
			#pragma multi_compile_fwdadd
			// Use the line below to add shadows for point and spot lights
//			#pragma multi_compile_fwdadd_fullshadows

			#pragma vertex vert
			#pragma fragment frag

			#include "Lighting.cginc"
			#include "AutoLight.cginc"

			fixed4 _Diffuse;
			fixed4 _Specular;
			float _Gloss;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f {
				float4 position : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
			};

			v2f vert(a2v v) {
				v2f o;
				o.position = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				#ifdef USING_DIRECTIONAL_LIGHT
					fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
				#else
					fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz - i.worldPos.xyz);
				#endif

				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * max(0, dot(worldNormal, worldLightDir));

				fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
				fixed3 halfDir = normalize(worldLightDir + viewDir);
				fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);

				#ifdef USING_DIRECTIONAL_LIGHT
					fixed atten = 1.0;
				#else
					float3 lightCoord = mul(unity_WorldToLight, float4(i.worldPos, 1)).xyz;
					fixed atten = tex2D(_LightTexture0, dot(lightCoord, lightCoord).rr).UNITY_ATTEN_CHANNEL;
				#endif

				return fixed4((diffuse + specular) * atten, 1.0);
			}

			ENDCG
		}
	}
	FallBack "Specular"
}
```

可见,新增`#include "AutoLight.cginc"`, 添加了一些阴影处理的宏定义  
v2f新增寄存器`SHADOW_COORDS(2)`, 这里的2是你要使用的寄存器(TEXCOORD)索引  
返回顶点之前使用`TRANSFER_SHADOW(o);`为这个寄存器赋值,也就是计算阴影纹理坐标  
片元着色器获得阴影值`fixed shadow = SHADOW_ATTENUATION(i);`  
输出`return fixed4(ambient + (diffuse + specular) * atten * shadow, 1.0);`

> 需要注意, 这些宏中使用上下文变量来进行计算, 所以必须使用 `vertex`,作为顶点, v2f必须命名为v, v2f中顶点必须命名为pos.

> 此代码未对AddPass做处理, 所以点光源还没有阴影.

一次性计算(atten * shadow)如下
```HLSL
//fixed atten = 1.0;

//fixed shadow = SHADOW_ATTENUATION(i); //先注释之前的这两个数据
UNITY_LIGHT_ATTENUATION(atten, i, i.worldPos)//使用Unity的宏

return fixed4(ambient + (diffuse + specular) * atten /** shadow*/, 1.0);//直接使用atten即可
```

> 使用`# pargma multi_compile_fwdadd_fullsadows`来代替之前的`#pragma multi_compile_fwdadd`, 这样AddPass也可以计算逐像素光源的阴影了

## 透明物体的阴影

无法直接的使用Fallback来处理

透明度测试
```HLSL

Shader "UnityShaderBook/9451"
{
	Properties
	{
		_Color("Color Tint", Color) = (1, 1, 1, 1)
		_MainTex("Main Tex", 2D) = "white" {}
		_Cutoff("Alpha Cutoff", Range(0, 1)) = 0.5
	}
	SubShader{
		Tags {"Queue" = "AlphaTest" "IgnoreProjector" = "True" "RenderType" = "TransparentCutout"}

		Pass 
		{
			Tags { "LightMode" = "ForwardBase" }

			Cull Off

			CGPROGRAM

			#pragma multi_compile_fwdbase

			#pragma vertex vert
			#pragma fragment frag

			#include "Lighting.cginc"
			#include "AutoLight.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _Cutoff;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
				float2 uv : TEXCOORD2;
				SHADOW_COORDS(3)
			};

			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);

				// Pass shadow coordinates to pixel shader
				TRANSFER_SHADOW(o);

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

				fixed4 texColor = tex2D(_MainTex, i.uv);

				clip(texColor.a - _Cutoff);

				fixed3 albedo = texColor.rgb * _Color.rgb;

				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;

				fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(worldNormal, worldLightDir));

				// UNITY_LIGHT_ATTENUATION not only compute attenuation, but also shadow infos
				UNITY_LIGHT_ATTENUATION(atten, i, i.worldPos);

				return fixed4(ambient + diffuse * atten, 1.0);
			}

			ENDCG
		}
	}
	FallBack "Transparent/Cutout/VertexLit"//正常
	//FallBack "Specular"//显示不正常
}
```

基本没什么更改, 只是FallBack变了. 它要求必须提供_Cutoff同名属性, 否则无法得到正确结果.

透明度混合
```HLSL

Shader "UnityShaderBook/9452"
{	Properties
	{
		_Color("Color Tint", Color) = (1, 1, 1, 1)
		_MainTex("Main Tex", 2D) = "white" {}
		_AlphaScale("Alpha Scale", Range(0, 1)) = 1
	}
	SubShader
	{
		Tags {"Queue" = "Transparent" "IgnoreProjector" = "True" "RenderType" = "Transparent"}

		Pass {
			Tags { "LightMode" = "ForwardBase" }

			ZWrite Off
			Blend SrcAlpha OneMinusSrcAlpha

			CGPROGRAM

			#pragma multi_compile_fwdbase

			#pragma vertex vert
			#pragma fragment frag

			#include "Lighting.cginc"
			#include "AutoLight.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _AlphaScale;

			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f {
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
				float3 worldPos : TEXCOORD1;
				float2 uv : TEXCOORD2;
				SHADOW_COORDS(3)
			};

			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);

				o.worldNormal = UnityObjectToWorldNormal(v.normal);

				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);

				// Pass shadow coordinates to pixel shader
				TRANSFER_SHADOW(o);

				return o;
			}

			fixed4 frag(v2f i) : SV_Target {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

				fixed4 texColor = tex2D(_MainTex, i.uv);

				fixed3 albedo = texColor.rgb * _Color.rgb;

				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;

				fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(worldNormal, worldLightDir));

				// UNITY_LIGHT_ATTENUATION not only compute attenuation, but also shadow infos
				UNITY_LIGHT_ATTENUATION(atten, i, i.worldPos);

				return fixed4(ambient + diffuse * atten, texColor.a * _AlphaScale);
			}

			ENDCG
		}
	}
	FallBack "Transparent/VertexLit"
	// Or  force to apply shadow
	//	FallBack "VertexLit"
}
```
为透明度混合提供阴影很难, 因为必须遵守从后往前的顺序渲染, 很复杂且印象性能.  
Unity中, 所有内置的半透明Shader都不生成阴影. 我么可与强制使用不透明物体的FallBack`FallBack "VertexLit"`来生成阴影, 但效果不是很好.

## 标准Shader

课程资料提供  
后续课程使用

> 都是已经学过的知识 还蛮简单的

Phong模型
```HLSL
Shader "UnityShaderBook/951"
{
	Properties 
	{
		_Color ("Color Tint", Color) = (1, 1, 1, 1)
		_MainTex ("Main Tex", 2D) = "white" {}
		_BumpMap ("Normal Map", 2D) = "bump" {}
	}
	SubShader 
	{
		Tags { "RenderType"="Opaque" "Queue"="Geometry"}

		Pass 
		{ 
			Tags { "LightMode"="ForwardBase" }
		
			CGPROGRAM
			
			#pragma multi_compile_fwdbase
			
			#pragma vertex vert
			#pragma fragment frag
			
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			
			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;
				float4 texcoord : TEXCOORD0;
			};
			
			struct v2f {
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float4 TtoW0 : TEXCOORD1;  
				float4 TtoW1 : TEXCOORD2;  
				float4 TtoW2 : TEXCOORD3;
				SHADOW_COORDS(4)
			};
			
			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				
				o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
				o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;
				
				float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;  
				fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);  
				fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);  
				fixed3 worldBinormal = cross(worldNormal, worldTangent) * v.tangent.w; 
				
				o.TtoW0 = float4(worldTangent.x, worldBinormal.x, worldNormal.x, worldPos.x);
				o.TtoW1 = float4(worldTangent.y, worldBinormal.y, worldNormal.y, worldPos.y);
				o.TtoW2 = float4(worldTangent.z, worldBinormal.z, worldNormal.z, worldPos.z);  
				
				TRANSFER_SHADOW(o);
				
				return o;
			}
			
			fixed4 frag(v2f i) : SV_Target {
				float3 worldPos = float3(i.TtoW0.w, i.TtoW1.w, i.TtoW2.w);
				fixed3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));
				
				fixed3 bump = UnpackNormal(tex2D(_BumpMap, i.uv.zw));
				bump = normalize(half3(dot(i.TtoW0.xyz, bump), dot(i.TtoW1.xyz, bump), dot(i.TtoW2.xyz, bump)));
				
				fixed3 albedo = tex2D(_MainTex, i.uv.xy).rgb * _Color.rgb;
				
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
			
			 	fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(bump, lightDir));
				
				UNITY_LIGHT_ATTENUATION(atten, i, worldPos);
				
				return fixed4(ambient + diffuse * atten, 1.0);
			}
			
			ENDCG
		}
		
		Pass 
		{ 
			Tags { "LightMode"="ForwardAdd" }
			
			Blend One One
		
			CGPROGRAM
			
			#pragma multi_compile_fwdadd
			// Use the line below to add shadows for point and spot lights
//			#pragma multi_compile_fwdadd_fullshadows
			
			#pragma vertex vert
			#pragma fragment frag
			
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			
			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;
				float4 texcoord : TEXCOORD0;
			};
			
			struct v2f {
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float4 TtoW0 : TEXCOORD1;  
				float4 TtoW1 : TEXCOORD2;  
				float4 TtoW2 : TEXCOORD3;
				SHADOW_COORDS(4)
			};
			
			v2f vert(a2v v) {
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				
				o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
				o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;
				
				float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;  
				fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);  
				fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);  
				fixed3 worldBinormal = cross(worldNormal, worldTangent) * v.tangent.w; 
				
				o.TtoW0 = float4(worldTangent.x, worldBinormal.x, worldNormal.x, worldPos.x);
				o.TtoW1 = float4(worldTangent.y, worldBinormal.y, worldNormal.y, worldPos.y);
				o.TtoW2 = float4(worldTangent.z, worldBinormal.z, worldNormal.z, worldPos.z);  
				
				TRANSFER_SHADOW(o);
				
				return o;
			}
			
			fixed4 frag(v2f i) : SV_Target {
				float3 worldPos = float3(i.TtoW0.w, i.TtoW1.w, i.TtoW2.w);
				fixed3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));
				
				fixed3 bump = UnpackNormal(tex2D(_BumpMap, i.uv.zw));
				bump = normalize(half3(dot(i.TtoW0.xyz, bump), dot(i.TtoW1.xyz, bump), dot(i.TtoW2.xyz, bump)));
				
				fixed3 albedo = tex2D(_MainTex, i.uv.xy).rgb * _Color.rgb;
				
			 	fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(bump, lightDir));
				
				UNITY_LIGHT_ATTENUATION(atten, i, worldPos);
				
				return fixed4(diffuse * atten, 1.0);
			}
			
			ENDCG
		}
	} 
	FallBack "Diffuse"
}
```

Blinn-Phong模型
```HLSL
Shader "UnityShaderBook/952"
{
	Properties 
	{
		_Color ("Color Tint", Color) = (1, 1, 1, 1)
		_MainTex ("Main Tex", 2D) = "white" {}
		_BumpMap ("Normal Map", 2D) = "bump" {}
		_Specular ("Specular Color", Color) = (1, 1, 1, 1)
		_Gloss ("Gloss", Range(8.0, 256)) = 20
	}
	SubShader 
	{
		Tags { "RenderType"="Opaque" "Queue"="Geometry"}
		
		Pass 
		{ 
			Tags { "LightMode"="ForwardBase" }
		
			CGPROGRAM
			
			#pragma multi_compile_fwdbase	
			
			#pragma vertex vert
			#pragma fragment frag
			
			#include "UnityCG.cginc"
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			fixed4 _Specular;
			float _Gloss;
			
			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;
				float4 texcoord : TEXCOORD0;
			};
			
			struct v2f {
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float4 TtoW0 : TEXCOORD1;  
                float4 TtoW1 : TEXCOORD2;  
                float4 TtoW2 : TEXCOORD3; 
				SHADOW_COORDS(4)
			};
			
			v2f vert(a2v v) {
			 	v2f o;
			 	o.pos = UnityObjectToClipPos(v.vertex);
			 
			 	o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
			 	o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;

				TANGENT_SPACE_ROTATION;
				
				float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;  
                fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);  
                fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);  
                fixed3 worldBinormal = cross(worldNormal, worldTangent) * v.tangent.w; 
                
                o.TtoW0 = float4(worldTangent.x, worldBinormal.x, worldNormal.x, worldPos.x);  
                o.TtoW1 = float4(worldTangent.y, worldBinormal.y, worldNormal.y, worldPos.y);  
                o.TtoW2 = float4(worldTangent.z, worldBinormal.z, worldNormal.z, worldPos.z);  
  				
  				TRANSFER_SHADOW(o);
			 	
			 	return o;
			}
			
			fixed4 frag(v2f i) : SV_Target {
				float3 worldPos = float3(i.TtoW0.w, i.TtoW1.w, i.TtoW2.w);
				fixed3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));
				
				fixed3 bump = UnpackNormal(tex2D(_BumpMap, i.uv.zw));
				bump = normalize(half3(dot(i.TtoW0.xyz, bump), dot(i.TtoW1.xyz, bump), dot(i.TtoW2.xyz, bump)));

				fixed3 albedo = tex2D(_MainTex, i.uv.xy).rgb * _Color.rgb;
				
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				
			 	fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(bump, lightDir));
			 	
			 	fixed3 halfDir = normalize(lightDir + viewDir);
			 	fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(bump, halfDir)), _Gloss);
			
				UNITY_LIGHT_ATTENUATION(atten, i, worldPos);

				return fixed4(ambient + (diffuse + specular) * atten, 1.0);
			}
			
			ENDCG
		}
		
		Pass { 
			Tags { "LightMode"="ForwardAdd" }
			
			Blend One One
		
			CGPROGRAM
			
			#pragma multi_compile_fwdadd
			// Use the line below to add shadows for point and spot lights
//			#pragma multi_compile_fwdadd_fullshadows
			
			#pragma vertex vert
			#pragma fragment frag
			
			#include "Lighting.cginc"
			#include "AutoLight.cginc"
			
			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			sampler2D _BumpMap;
			float4 _BumpMap_ST;
			float _BumpScale;
			fixed4 _Specular;
			float _Gloss;
			
			struct a2v {
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 tangent : TANGENT;
				float4 texcoord : TEXCOORD0;
			};
			
			struct v2f {
				float4 pos : SV_POSITION;
				float4 uv : TEXCOORD0;
				float4 TtoW0 : TEXCOORD1;  
                float4 TtoW1 : TEXCOORD2;  
                float4 TtoW2 : TEXCOORD3;
				SHADOW_COORDS(4)
			};
			
			v2f vert(a2v v) {
			 	v2f o;
			 	o.pos = UnityObjectToClipPos(v.vertex);
			 
			 	o.uv.xy = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
			 	o.uv.zw = v.texcoord.xy * _BumpMap_ST.xy + _BumpMap_ST.zw;

				float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;  
                fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);  
                fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);  
                fixed3 worldBinormal = cross(worldNormal, worldTangent) * v.tangent.w; 
	
  				o.TtoW0 = float4(worldTangent.x, worldBinormal.x, worldNormal.x, worldPos.x);
			  	o.TtoW1 = float4(worldTangent.y, worldBinormal.y, worldNormal.y, worldPos.y);
			  	o.TtoW2 = float4(worldTangent.z, worldBinormal.z, worldNormal.z, worldPos.z);  
			 	
			 	TRANSFER_SHADOW(o);
			 	
			 	return o;
			}
			
			fixed4 frag(v2f i) : SV_Target {
				float3 worldPos = float3(i.TtoW0.w, i.TtoW1.w, i.TtoW2.w);
				fixed3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));
				fixed3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));
				
				fixed3 bump = UnpackNormal(tex2D(_BumpMap, i.uv.zw));
				bump = normalize(half3(dot(i.TtoW0.xyz, bump), dot(i.TtoW1.xyz, bump), dot(i.TtoW2.xyz, bump)));
				
				fixed3 albedo = tex2D(_MainTex, i.uv.xy).rgb * _Color.rgb;
				
			 	fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(bump, lightDir));
			 	
			 	fixed3 halfDir = normalize(lightDir + viewDir);
			 	fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(bump, halfDir)), _Gloss);
			
				UNITY_LIGHT_ATTENUATION(atten, i, worldPos);

				return fixed4((diffuse + specular) * atten, 1.0);
			}
			
			ENDCG
		}
	} 
	FallBack "Specular"
}
```

# 高级纹理

## 立方体纹理

### 天空盒子

### 环境映射的立方体纹理
### 反射
### 折射

## 渲染纹理

### 镜子效果

### 玻璃效果

### 渲染纹理 vs.GrabPass

## 程序纹理

### Unity中的程序纹理

### Unity的程序材质

# 完毕


**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
