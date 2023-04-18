---
title: 上帝视角看GPU
date: 2023-04-16 00:55:00
updated: 2023-04-16 00:55:00
id: ml-20230416-005500-g184
categories:
	- Unity
	- TA
tags: 
	- Unity
	- TA
	- Shader
	- GPU
---

专注于GPU本身

<!--more-->

# 图形流水线基础

## 显示

`显示器`以`像素`为基本单元显示画面  
常见的情况下, 每个`像素`包含红绿蓝, 即RBG三个分量, 每个分量8位, 即0-255区间  

> 随着时间推移, 有些显示器可以使用更多位表示颜色, 但具体的细节还是类似  
> > 本次以8位做讲解

显示器每次从`帧缓冲区(Frame Buffer)`读取等待显示的像素列表  
这意味着帧缓冲区的内容与显示器的显示内容数据结构上是一致的  
由于32位处理更快速, 所以一半有RGBA每个8位, 共计32位, 即使`A(Alpha)`会被显示器忽略

> 此技术主要用于防止以避免屏幕被撕裂, 即用户看到刷新到一半的内容  
> > 扩展阅读 `垂直同步`

此时就有了原始的显卡(Graphics Card), 即图像的搬运工, 将帧缓冲区的内容发送给显示器  
仅仅只是一个图像的搬运工

## 图像处理

一个新的需求诞生了, 需要将图像的RGB都乘以2之后显示  
固然可以在CPU上给每个像素值操作, 但性能上更好的方法是在搬运图像前新增一个像素处理单元

灵活多变的需求催生出了`Shader`, 此处是`PixelShader`  
这种可添加Shader的单元叫做`可编程流水线单元(Programmable Pipeline Unit)`  
每个Pixel仅处理一个像素, 单入单出, 无疑拥有更高的效率

此时, PixelShader的参数是一个坐标, 依据坐标从图像上采集颜色, 执行操作后返回
> 具体参数的使用方式, 取决于程序的编写者

输入的图像称作`纹理(Texture)`

由专门的硬件负责从内存中读取纹理, 运行Shader代码, 存放到帧缓存区域  
这便是针对图像的处理器, 可称为`Picture Processing Unit`

## 几何网格(模型)处理

将简单网格(点, 线, 三角形), 称之为`图元(Primitive)`  
存放`顶点(Vertex)`的`Buffer`称为`Vertex Buffer`  
链接顶点的线存储为一个整数, 表示Vertex的索引, 称其`Index Buff`

将这些顶点和线组装成三角形, 然后就可以开始处理光栅化的流程  
裁剪屏幕外三角形, 计算三条边的方程等  
然后送入`Rasterizer`  
此步骤称为`图元组装单元(Primitive Assembler)`

同一个几何体, 可以变换位置与角度.  
因为几何本身的拓扑关系不变, 所以被存储的顶点和线也不会改变  
这里就涉及到变换, 将几何体从几何空间变换到屏幕空间  
即将`(x,y,z,1)`变换为`(x',y',z',w')`

* World Matrix
  * 位置, 朝向, 缩放
  * 世界坐标
  * 将物体放置与一个全局的世界中
* View Matrix
  * 摄像机的位置
  * 摄像机坐标, 又称观察者坐标
  * 将物体放置在以摄像机为原点的坐标系中
* Projection Matrix
  * 摄像机参数, 入视野宽窄, 视域远近
  * 投影, 投影矩阵, 投影坐标
  * 将物体显示在屏幕范围空间, 并带有近大远小

很显然, 这是固定不变的算法, 也适合使用硬件处理解决  
称之为`硬件变换与光照(T&L)`  
又称`Transforming以及Lighting`, `光影转换`

后续由于需求多样性, 也开放了可编程的部分`VertexShader`  
即对每个顶点的处理, 顶点信息有多种保存方式, VertexShader也无需知道格式  
需要调用者提供一个格式的表述, 比如`单缓存布局(Single Buff Layout)`, `多缓存布局(Multiple Buffer Layout)`  
当然, 最后由一个固定的流水线单元, 即硬件部分, 称为`Input Assembler`  
从VertexBuff里组装出一个顶点, 动刀VertexShader处理

如此, 每个VertexShader也是单入单出的模式

自此以得到一条基本的`图形流水线(Graphice Pipeline)`  
调用者提供输入的信息后, 就能流程化的输出图像, 并送给显示器  
流水线中由可编程带院和不可编程单元组成, 保证效率的同时提供一些灵活性

这便是2000年代主流GPU的构成  
这将CPU从复杂大量的操作中解放出来  
由专业的`GPU(Graphics Processing Unit)`来并行处理

## 小结

可以看到不论是PixelShader还是VertexShader都用于接受并处理数据  
他们并不关心如何从内存读取, 处理完毕后如何写出, 前后都有固定的流水线来处理  
看起来, Shader就像回调函数, 由GPU在需要处理大量数据时, 每个单元调用一下

此时, 可以看到CPU和GPU的差别了  
CPU擅长在小数据上做相对复杂的串行处理和逻辑操作  
GPU擅长在大量数据上做相对简单的并行处理  
这些特点使得他们可以分工协作, 更有效率


# 逻辑上的模块划分

## 细分

随需求又有新的`GeometryShader`, 相当于拆开`图元组装单元(Primitive Assembler)`  
此Shader可以单入多出, 即一个Primitive变为多个Primitive  
由此可将三角形移动或切成多个(自定义个数)  
开启了GPU可处理非均匀输入输出的任务先例  
但此Shader并非必须, 可退化为Primitive Assembler  

然后, GeometryShader可以直接导出数据到内存, 这也相等于其前置工序`Early Primitive Assembler`也同时拥有这项能力  
提供了从流水线中直接导出数据的能力  
典型的应用场景是, 存储中间数据, 减少重复计算

但是使用后发现, 性能是很低的  
因为灵活, 硬件难以做出针对性优化, 实现的非常保守  
硬件在执行之前甚至不知道你要处理的是三角形

于是, 随着三角形细分需求的增加, 逐渐衍生出在VertexShader之后的`Tessellator`工序  

* `Hull Shader` 指定图元如何细分
  * 内部分出多少分
  * 每条边分成多少段
* `Tessellator`
  * 细分算法
* `domain Shader` 计算细分后的顶点信息

这个工序也是可选的

## 通用计算

于是之后GPU做通用计算的能力开始大幅发展  
最早是先渲染一个覆盖屏幕的大三角形, 在PixelShader内做并行计算  
但是这还是单入淡出, 且需要经过整条流水线, 存在浪费, 而且学习成本也很高  
此方向称为`GPGPU`, 即`通用图形处理器（General-purpose computing on graphics processing units)`

后续催生出可多入多出, 可任意读取写入的, 不经过流水线的单元`Comppute Shader`  
独立于流水线单独存在

## 自动生成

后续优化方向是, 少数入甚至不输入数据, GPU可以自行生成顶点  
于是有了`Amplification Shader`, `Mesh Shader`  
其中AmplificationShader负责指定执行多少次MeshShader  
MeshShader负责产生几何体  
此时渲染的不再是图元, 而是一小块网格, 称为`meshlet`

## 光线追踪

再后面就是光线追踪, 这是一条独立的流水线, 包含多个新的Shader  
* `Ray Generation Shader` 生成光线
* `Intersection Shader` 判定光线与物体是否相交
* `Any Hit Shader` 光线打到物体上时是否可继续前进
* `Closest Hit Shader` 光线打到物体的最近点计算颜色
* `Miss Shader` 光线未打到任意点时计算颜色
* `Callable Shader` 动态调用

## 更多流水线

同样的流水线扩展思路, 还可用于更多领域
* `Tensor Core` 神经网络计算, 张量核
* `Video Codec` 视频编码解码
* 等...

## 更多展望

CPU是一个通用模块  
GPU则有多条专用流水线, 需要有具体了解才能再程序中开发  
目前GPU各个流水线不能互相调用, 如果有这种需要, 则需经过内存中转

目前GPU有区分为图像计算(GPU)和神经网络计算(GPGPU)  
实际上图像计算单元也可作为通用计算单元的高效补充  
比如Rasterizer可作为高效的插值器  
OutputMerger可作为高效的数据累加器

> GPU连连看是否可能实现? 硬件层面如何处理

## 小结

本节讲解了GPU多重流水线的扩展过程  
但是目前有如此之多的Shader, 很容易遇到负载均衡问题  
而且流水线复杂度提高, 硬件的生产成本也是个问题

# 部署到硬件

# 完整的软件栈

# 图形流水线里的不可编程单元

# 光线跟踪流水线


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
