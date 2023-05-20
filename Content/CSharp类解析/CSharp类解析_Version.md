---
title: CSharp类解析_Version
date: 2023-05-20 17:24:00
updated: 2023-05-20 17:24:00
id: ml-20230520-172400-g186
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

`Version` 在`DotNet`版本信息类型,在System中被广泛应用  

> TODO 新版本还新增了 `IFormattable  ISpanFormattable`接口实现, 

<!--more-->

官方文档定义: 表示程序集、操作系统或公共语言运行时的版本号。 此类不能被继承

其参数的必要性`major.minor[.build[.revision]]`

组件按约定使用，如下所示：
* 主要：名称相同但主版本不同的程序集不可互换。 较高的版本号可能表示无法假定向后兼容性的产品发生重大重写。
* 次要：如果两个程序集的名称和主版本号相同，但次要版本号不同，则表示具有向后兼容性的显著增强。 此较高的次要版本号可能表示产品的点版本或产品的完全向后兼容新版本。
* 生成：生成号的差异表示对同一源的重新编译。 处理器、平台或编译器更改时，可能会使用不同的内部版本号。
* 修订版：具有相同名称、主版本号和次要版本号但不同修订的程序集旨在完全可互换。 在修复以前发布的程序集中的安全漏洞的版本中，可能会使用更高的修订号。

> 从 `.NET Framework 2.0` 开始， `MajorRevision` 和 `MinorRevision` 属性使你能够识别应用程序的临时版本，例如，可以更正问题，直到可以发布永久解决方案。 此外，`Windows NT操作系统`使用 `MajorRevision` 属性对 `Service Pack` 编号进行编码


以下示例演示了一些最常见的方案
*  `OperatingSystem.Version` 属性检索操作系统的版本号
*  `Environment.Version` 属性检索有关公共语言运行时的版本信息
*  `AssemblyName.Version` 检索程序集版本信息
*  `ApplicationDeployment.CurrentVersion`属性检索`ClickOnce`应用程序的发布版本

---


**注意, 其`HashCode`有可能相同, 详见`GetHashCode()`函数**  
如需使用其`HashCode`作为唯一凭证, 需统计+测试用例以防止重复  
推荐起始数值至少都是1, 可明显提升生成的Hash值大小, 至少避免了生成的Hash=0, 或=1的问题.  
但仍无法避免最低`4096次Revision`或`Minor+256`或`Build`或`Major+16`便会相同的问题

读源码后可见, 是围绕4个用于表示版本的变量, 构筑了一套排序,比较,转化,等周边内容的组合.
```CSharp
//实现各种接口
//ICloneable, IComparable,IComparable<Version>, IEquatable<Version>

//数据-当然, 你可以随意使用, 但以下是一般性的解释(可用于插件,Assembly等.)
private int _Major;//主版本号, 有重大修改, 无法向后兼容
private int _Minor;//次版本号, 有显著修改, 可以向后兼容
private int _Build = -1;//重新构建, 一般每次生成最终成品时+1
private int _Revision = -1;//修复漏洞,优化体验, 每次生成最终成品时增加数量不定, 取决于有多少提交
//构造- 用于初始化, 且这些数据不能小于0
public Version(int major, int minor, int build, int revision);
//由于_Build和_Revision有初始值, 所以有三个构造来支持初始值不同的情况
public Version(int major, int minor, int build);
public Version(int major, int minor); 
//String实际上由Version.Parse变为一个Version, 然后将其值赋给当前的Version
//注意, Version是引用类型, 此次不是赋值指针, 而是使用数据生成新的类
public Version(String version) ;
//_Major=0,_Minor=0
public Version() ;
//封装属性, 仅Get
public int Major {
    get { return _Major; }
}
public int Minor {
    get { return _Minor; }
}
public int Build {
    get { return _Build; }
}
public int Revision {
    get { return _Revision; }
}
//高低位
public short MajorRevision {
    get { return (short)(_Revision >> 16); }
}
public short MinorRevision {
    get { return (short)(_Revision & 0xFFFF); }
}
//实现ICloneable接口, New一个新的, 然后把四个变量赋值
public Object Clone();
//实现IComparable接口
public int CompareTo(Object version)
{
    //入参等于null, return 1, 即this较大
    //as转化后不等于null
    //按顺序比较 _Major, _Minor, _Build, _Revision, 大的返回1, 小的返回0, 都相等返回0
}
//实现IComparable<T>接口, 与IComparable接口相同, 但注意,不是调用CompareTo(Object version), 重写了一遍
public int CompareTo(Version value)
{
    //入参等于null, return 1, 即this较大
    //按顺序比较 _Major, _Minor, _Build, _Revision, 大的返回1, 小的返回0, 都相等返回0
}
//重载object的Equals, 比较四个变量是否相等
public override bool Equals(Object obj);
//实现IEquatable, 比较四个变量是否相等, 注意不是调用上面重载的obj的比较, 重写了一遍
public bool Equals(Version obj);
//重载Obj的GetHashCode
public override int GetHashCode()
{
    //注意-人为控制Hash相等如下
    //Log($"{new Version(0, 0, 0, 0).GetHashCode()} | {new Version(16, 256, 256, 4096).GetHashCode()}"); //OutPut `0 | 0`
    //Log($"{new Version(0, 0, 0, 1).GetHashCode()} | {new Version(16, 256, 256, 4097).GetHashCode()}"); //OutPut `1 | 1`
    //Log($"{new Version(1, 1, 1, 1).GetHashCode()} | {new Version(17, 257, 257, 4097).GetHashCode()}"); //OutPut `269488129 | 269488129`

    //每个变量取一小段, 然后偏移相加
    accumulator |= (this._Major & 0x0000000F) << 28;
    accumulator |= (this._Minor & 0x000000FF) << 20;
    accumulator |= (this._Build & 0x000000FF) << 12;
    accumulator |= (this._Revision & 0x00000FFF);
}
//返回 Major + "." + _Minor + "." + _Build + "." + _Revision`, 如有默认参数, 最低生成String.Concat(_Major,".",_Minor)
public override String ToString();
//注意, 此ToString也是Public的, 外界可调用, fieldCount是输出4个变量中的几个
public String ToString(int fieldCount);
//=========成员截止, 后续是静态
//new一个VersionResult, 调用TryParseVersion, 不成功则throw
public static Version Parse(string input);
//同上, 但不引发异常
public static bool TryParse(string input, out Version result);
//传入待转化的string, 返回转化是否成功与VersionResult
private static bool TryParseVersion(string version, ref VersionResult result)
{
    //string == null, SetFailure(ArgumentNullException);
    //使用Split('.'), Length<2 或 >4, SetFailure(ArgumentException);
    //调用TryParseComponent逐个得到每个参数(major, minor, build, revision)的值
    //手动result.m_parsedVersion = new Version(major, minor, build, revision);
}
//实际调用Int32.TryParse, 并视异常情况VersionResult.SetFailure
private static bool TryParseComponent(string component, string componentName, ref VersionResult result, out int parsedComponent);
//重载运算符 -注意, 一下运算符代码忽视了一些异常检测, 以凸显重点
public static bool operator ==(Version v1, Version v2) {
    return v1.Equals(v2);
}

public static bool operator !=(Version v1, Version v2) {
return !(v1 == v2);//这里使用了重载==
}

public static bool operator <(Version v1, Version v2) {
return (v1.CompareTo(v2) < 0);
}

public static bool operator <=(Version v1, Version v2) {
return (v1.CompareTo(v2) <= 0);
}

public static bool operator >(Version v1, Version v2) {
return (v2 < v1);//这里使用了重载<
}

public static bool operator >=(Version v1, Version v2) {
return (v2 <= v1);//这里使用了重载<=
}
//=========静态截止, 后续是内部类(结构)
//转化过程中的异常枚举
internal enum ParseFailureKind 
{ 
    ArgumentNullException, 
    ArgumentException, 
    ArgumentOutOfRangeException, 
    FormatException 
}
//用于转化 String2Version
internal struct VersionResult
{
    internal Version m_parsedVersion;
    internal ParseFailureKind m_failure;
    internal string m_exceptionArgument;
    internal string m_argumentName;
    internal bool m_canThrow;
    //初始化2个参数
    internal void Init(string argumentName, bool canThrow);
    //实际是SetFailure(failure, String.Empty);
    internal void SetFailure(ParseFailureKind failure);
    //赋值另外2个参数, 如果m_canThrow, 依据m_failure引发异常, default实际也是引发异常
    internal void SetFailure(ParseFailureKind failure, string argument);
}
```


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
