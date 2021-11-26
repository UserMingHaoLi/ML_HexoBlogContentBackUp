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

```HLSL
float3  fVector = { 0.2f, 0.3f, 0.4f };
```

也可使用此语法声明向量
```HLSL
vector <double, 4> dVector = { 0.2, 0.3, 0.4, 0.5 };
```

矢量最多包含四个组件，每个组件都可以使用以下两个命名集之一进行访问

* 位置集： x、y、z 和 w
* 颜色集： r、g、b、a

```HLSL
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

```HLSL
float2x2 fMatrix = { 0.0f, 0.1, // row 1
                     2.1f, 2.2f // row 2
                   };   
```

也可使用此语法声明矩阵

```HLSL
matrix <float, 2, 2> fMatrix = { 0.0f, 0.1, // row 1
                                 2.1f, 2.2f // row 2
                               };
```

下面的示例使用两个命名集来访问矩阵

```HLSL
// given
float2x2 fMatrix = { 1.0f, 1.1f, // row 1
                     2.0f, 2.1f  // row 2
                   }; 

float f_1D;
f_1D = matrix._m00; // read the value in row 1, column 1: 1.0
f_1D = matrix._m11; // read the value in row 2, column 2: 2.1

f_1D = matrix._11;  // read the value in row 1, column 1: 1.0
f_1D = matrix._22;  // read the value in row 2, column 2: 2.1

//与矢量一样，命名集可以使用命名集中的一个或多个组件
// Given
float2x2 fMatrix = { 1.0f, 1.1f, // row 1
                     2.0f, 2.1f  // row 2
                   };
float2 temp;

temp = fMatrix._m00_m11 // valid
temp = fMatrix._m11_m00 // valid
temp = fMatrix._11_22   // valid
temp = fMatrix._22_11   // valid
```

从零开始的行列位置：
* _m00、 _ m01、 _ m02、 _ m03
* _m10、 _ m11、 _ m12、 _ m13
* _m20、 _ m21、 _ m22、 _ m23
* _m30、 _ m31、 _ m32-16ms、 _ m33
从1开始的行列位置：
* _11、 _ 12、 _ 13、 _ 14
* _21、 _ 22、 _ 23、 _ 24
* _31、 _ 32、 _ 33、 _ 34
* _41、 _ 42、 _ 43、 _ 44

还可以通过使用数组访问表示法`[0][0]`

```HLSL
temp = fMatrix[0][0] // single component read
temp = fMatrix[0][1] // single component read
```

请注意，结构运算符 "." 不用于访问数组, 也不能同时访问多个数据

```HLSL
float2 temp;
temp = fMatrix[0][0]_[0][1] // 无效，无法读取两个组件

//但是，数组访问可以读取多组件向量
temp = fMatrix[0] // read the first row
```

> 可以使用关键字更改矩阵包装, 使其按行或者列存放于寄存器.这是一个重要的考虑因素

行-主矩阵的布局如下所示：  

 11 21 31 41  
 12 22 32 42  
 13 23 33 43  
 14 24 34 44  

列主矩阵的布局如下所示：  
 11 12 13 14  
 21 22 23 24  
 31 32 33 34  
 41 42 43 44  

> 默认情况下，统一参数的矩阵封装顺序设置为列-主

# 运算

默认情况下，统一参数的矩阵封装顺序设置为列-主

```HLSL
float4 v = a*b;
```

等同于

```HLSL
v.x = a.x*b.x;
v.y = a.y*b.y;
v.z = a.z*b.z;
v.w = a.w*b.w;
```

矢量 * 矩阵 如下转化

```HLSL
val.xyz = (float3) mul((float1x4)pos,World);
```
此示例使用 (float1x4) cast 将 pos 向量转换为列向量

# 结构

下面是一些示例结构声明
```HLSL
struct struct2
{
  int    a;
  float  b;
  int4x4 iMatrix;
}
```

> 也可以用户定义类型

# 在Unity中创建Shader

这是一个新建的`UnlitShader`,无任何修改.
```HLSL
Shader "Unlit/01MiniShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                UNITY_FOG_COORDS(1)
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                UNITY_TRANSFER_FOG(o,o.vertex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed4 col = tex2D(_MainTex, i.uv);
                // apply fog
                UNITY_APPLY_FOG(i.fogCoord, col);
                return col;
            }
            ENDCG
        }
    }
}
```

> 右键Shader,从此面板新建材质球(Material), 即可自动使用此Shader并自动命名

其中`Shader`块是主体, `Properties`块是暴露给Unity的参数,但想使用,还是得自己在`Pass`里面定义一个.  
`SubShader`官方文档说是可以作为多种硬件适配,此处只写一个即可, 内部的`Pass`就是一次渲染,也就是常说的`PassCall`,可以写多个Pass

可以看到`Pass`之前还有一些`Tags`,`LOD`这些是对于此`SubShader`的一些设置.

这样就是一个最小Shader代码.
```HLSL
Shader "CS0102/01MiniShader"
{
	Properties
	{
   
	}
	SubShader
	{
		Pass
		{

		}
	}
}
```

让后在`Properties`中曝光一些变量,也使用一些向量和矩阵  

```HLSL
Properties
{
    _Float("Float",Float) = 0.0
		_Range("Range",Range(0.0,1.0)) = 0.5
		_Vector("Vector",Vector) = (1,1,1,1)
		_Color("Color",Color) = (0.5,0.5,0.5,0.5)
    _Texture("Texture",2D) = "black"{}
}
```

按顺序来,`_Float`是变量在代码中的名字,可自行命名,以`_`起头是行业规定,并非强制要求.  
`"Float"`是外部Unity在Shader参数界面显示的名字,可自行命名  
`Float`是类型,和C#的`float`一样,是指浮点数, 后面就是赋值,其中浮点数行业规定要有小数点.

`Pass`写完之后如下
```HLSL
Properties
	{
		_MainTex("MainTex",2D) = "black"{}
		_Float("Float",Float) = 0
		_Range("Range",Range(0.0,1.0)) = 0.5
		_Vector("Vector",Vector) = (1,1,1,1)
		_Color("Color",Color) = (0.5,0.5,0.5,0.5)
	}
	SubShader
	{
		Pass
		{
			CGPROGRAM
			//定义顶点Shader
			# pragma vertex vert 
			//定义片元Shader
			# pragma fragment frag 
			//导入UnityCG命名空间
			#include "UnityCG.cginc" 
			//CPU输入值
			struct appdata
			{
				float4 vertex : POSITION;
				float2 uv : TEXCOORD0;//第一套uv,最多四套
				float3 normal : NORMAL;
				float color : COLOR;
			};
			//顶点Shader返回值
			struct v2f
			{
				float4 pos : SV_POSITION;
				float2 uv : TEXCOORD0;//非UV,是通用储存器,可自定义用于存储什么内容, 0-15共16个
			};
			float4 _Color; //从`Properties`拿过来的数据
			sampler2D _MainTex;

			//顶点Shader
			v2f vert(appdata v)
			{
				v2f o;
				//UNITY_INITIALIZE_OUTPUT(v2f, o); //初始化,消除警告
				////MVP矩阵转化模型空间为裁剪空间
				float4 pos_world = mul(unity_ObjectToWorld, v.vertex);
				float4 pos_view = mul(UNITY_MATRIX_V, pos_world);
				float4 pos_clip = mul(UNITY_MATRIX_P, pos_view);
				o.pos = pos_clip;
				o.uv = v.uv;
				return o;//返回的值将在片元Shader作为参数使用
			}

      //float 32位
      //half4 16位 UV,大部分向量
      //fixed 8位 颜色

			//片元Shader
			half4 frag(v2f f) : SV_Target
			{
				fixed4 col = tex2D(_MainTex, f.uv); //从MainTex采样, 使用f的uv信息
				//return _Color;//外界输入的纯色返回
				return col;//返回片元颜色信息
			}
			ENDCG
		}
```

其中`CGPROGRAM`和`ENDCG`对表示这处于CG程序内部.  

> 可以看到有一个警告`Output value 'vert' is not completely initialized`, 这需要我们初始化`vert`的返回值, 也就是在`v2f`后新增一句`UNITY_INITIALIZE_OUTPUT(v2f, o);`

完全体如下

```HLSL
Shader "CS02/MiniShader" //Shader的真正名字  可以是路径式的格式
{
	/*材质球参数及UI面板
	https://docs.unity3d.com/cn/current/Manual/SL-Properties.html
	https://docs.unity3d.com/cn/current/ScriptReference/MaterialPropertyDrawer.html
	https://zhuanlan.zhihu.com/p/93194054
	*/
	Properties 
	{
		_MainTex ("Texture", 2D) = "" {}
		_Float("Float", Float) = 0.0
		_Slider("Slider", Range(0.0,1.0)) = 0.07
		_Vector("Vector", Vector) = (.34, .85, .92, 1) 
	}
	/*
	这是为了让你可以在一个Shader文件中写多种版本的Shader，但只有一个会被使用。
	提供多个版本的SubShader，Unity可以根据对应平台选择最合适的Shader
	或者配合LOD机制一起使用。
	一般写一个即可
	*/
	SubShader
	{
		/*
		标签属性，有两种：一种是SubShader层级，一种在Pass层级
		https://docs.unity3d.com/cn/current/Manual/SL-SubShaderTags.html
		https://docs.unity3d.com/cn/current/Manual/SL-PassTags.html
		*/
		Tags { "RenderType"="Opaque" } 
		/*
		Pass里面的内容Shader代码真正起作用的地方，
		一个Pass对应一个真正意义上运行在GPU上的完整着色器(Vertex-Fragment Shader)
		一个SubShader里面可以包含多个Pass，每个Pass会被按顺序执行
		*/
		Pass 
		{
			CGPROGRAM  // Shader代码从这里开始
			#pragma vertex vert //指定一个名为"vert"的函数为顶点Shader
			#pragma fragment frag //指定一个名为"frag"函数为片元Shader
			#include "UnityCG.cginc"  //引用Unity内置的文件，很方便，有很多现成的函数提供使用

			//https://docs.unity3d.com/Manual/SL-VertexProgramInputs.html
			struct appdata  //CPU向顶点Shader提供的模型数据
			{
				//冒号后面的是特定语义词，告诉CPU需要哪些类似的数据
				float4 vertex : POSITION; //模型空间顶点坐标
				half2 texcoord0 : TEXCOORD0; //第一套UV
				half2 texcoord1 : TEXCOORD1; //第二套UV
				half2 texcoord2 : TEXCOORD2; //第二套UV
				half2 texcoord4 : TEXCOORD3;  //模型最多只能有4套UV

				half4 color : COLOR; //顶点颜色
				half3 normal : NORMAL; //顶点法线
				half4 tangent : TANGENT; //顶点切线(模型导入Unity后自动计算得到)
			};

			struct v2f  //自定义数据结构体，顶点着色器输出的数据，也是片元着色器输入数据
			{
				float4 pos : SV_POSITION; //输出裁剪空间下的顶点坐标数据，给光栅化使用，必须要写的数据
				float2 uv : TEXCOORD0; //自定义数据体
				//注意跟上方的TEXCOORD的意义是不一样的，上方代表的是UV，这里可以是任意数据。
				//插值器：输出后会被光栅化进行插值，而后作为输入数据，进入片元Shader
				//最多可以写16个：TEXCOORD0 ~ TEXCOORD15。
				float3 normal : TEXCOORD1;
			};

			/*
			Shader内的变量声明，如果跟上面Properties模块内的参数同名，就可以产生链接
			*/
			sampler2D _MainTex;
			float4 _MainTex_ST;
			//Unity内置变量：https://docs.unity3d.com/560/Documentation/Manual/SL-UnityShaderVariables.html
			//Unity内置函数：https://docs.unity3d.com/560/Documentation/Manual/SL-BuiltinFunctions.html
			
			//顶点Shader
			v2f vert (appdata v)
			{
				v2f o;
				float4 pos_world = mul(unity_ObjectToWorld, v.vertex);
				float4 pos_view = mul(UNITY_MATRIX_V, pos_world);
				float4 pos_clip = mul(UNITY_MATRIX_P, pos_view);
				o.pos = pos_clip;
				//o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
				//o.pos = UnityObjectToClipPos(v.vertex);
				o.uv = v.texcoord0 * _MainTex_ST.xy + _MainTex_ST.zw;
				//o.uv = TRANSFORM_TEX(v.uv, _MainTex);
				o.normal = v.normal;
				return o;
			}
			//片元Shader
			half4 frag (v2f i) : SV_Target //SV_Target表示为：片元Shader输出的目标地（渲染目标）
			{
				//fixed4 col = tex2D(_MainTex, i.uv);
				half4 col = float4(i.uv,0.0,0.0);
				return col;
			}
			ENDCG // Shader代码从这里结束
		}
	}
}
```

注意到, Unity帮我们做了些事情  
```HLSL
// Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
// Upgrade NOTE: replaced '_Object2World' with 'unity_ObjectToWorld'
```

默认的Shader是有背面剔除`Cull Back`, 想要不剔除, 需要在`CGPROGRAM`上方添加`Cull Off`  
正面剔除是`Cull Front`

可以将此属性曝光在`Properties`, 之后在`CGPROGRAM`上方添加`Cull [_CullMode]`, 如下  
```
[Enum(UnityEngine.Rendering.CullMode)]_CullMode("CullMode", float) = 2
```

由于UV的不同, 在平面表现良好的`tex2D(_MainTex, i.uv);`很难直接应用到3D模型.  
光栅化插值的时候,可能由于UV不连续导致不合理的值

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

[Unity_Documentation_Shader_Link]:https://docs.unity.cn/cn/current/Manual/shader-introduction.html "UnityShader文档"

[Microsoft_Documentation_HLSL_Link]:https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl "高级着色器语言 (HLSL)"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
