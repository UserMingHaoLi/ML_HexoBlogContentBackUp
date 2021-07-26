---
title: CSharp类解析_System_Collections杂项
date: 2021-07-25 23:50:00
updated: 2021-07-25 23:50:00
id: ml-20210725-235000-g147
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

可能有些会扩展,那就单独拿出来

<!--more-->

# BitArray

`true`和`false`的数组

```C#
BitArray myBA1 = new BitArray( 5 );
```

# CaseInsensitiveComparer

比较器,用于比较两个对象是否相等，比较时忽略字符串的大小写

> 不建议使用 `CaseInsensitiveComparer` 类进行新的开发。 相反，我们建议使用 `System.StringComparer StringComparer.CurrentCultureIgnoreCase` 、或属性返回的对象 `StringComparer.InvariantCultureIgnoreCase StringComparer.OrdinalIgnoreCase` 。

> 根据区域性，字符串比较可能会产生不同的结果

用作键的对象 `Hashtable` 需要重写 `Object.GetHashCode` 方法 (或 `IHashCodeProvider` 接口) 和 `Object.Equals` 方法 (或 `IComparer` 接口) 。 方法或接口的实现必须以相同的方式处理区分大小写。否则， `Hashtable` 可能行为不正确

# CaseInsensitiveHashCodeProvider

使用忽略字符串大小写的哈希算法，为对象提供哈希代码。

> 不建议使用 `CaseInsensitiveHashCodeProvider` 类进行新的开发,具体同上.

```C#
Hashtable myHT2 = new Hashtable( new CaseInsensitiveHashCodeProvider(), new CaseInsensitiveComparer() );
```

# CollectionBase

提供强类型集合的 `abstract` 基类

```C#
//派生
System.CodeDom.CodeAttributeArgumentCollection
System.CodeDom.CodeAttributeDeclarationCollection
System.CodeDom.CodeCatchClauseCollection
System.CodeDom.CodeCommentStatementCollection
System.CodeDom.CodeDirectiveCollection
```

> 不建议使用 `CollectionBase` 类进行新的开发。 相反，我们建议使用泛型 `Collection<T>` 类

# Comparer 

比较两个对象是否相等，其中字符串比较是区分大小写的

```C#
Comparer.DefaultInvariant.Compare( str1, str2 )
```

> 此类是`IComparer`接口的默认实现  

比较过程使用当前线程的。    
根据区域性，字符串比较可能会产生不同的结果

# DictionaryBase 

为键/值对的强类型集合提供 `abstract` 基类

```C#
//派生
System.Diagnostics.InstanceDataCollection
System.Diagnostics.InstanceDataCollectionCollection
System.DirectoryServices.ActiveDirectory.ActiveDirectoryReplicationMetadata
System.DirectoryServices.Protocols.SearchResultAttributeCollection
System.DirectoryServices.ResultPropertyCollection
```

# DictionaryEntry 

可设置或检索的字典键/值对

```C#
foreach (DictionaryEntry de in openWith)
```



# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
