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

# Hashtable 

表示根据键的哈希代码进行组织的键/值对的集合

不建议使用 `Hashtable` 类进行新的开发。 相反，我们建议使用泛型 `Dictionary<TKey,TValue>` 类

> 只有一个线程执行写入 (更新) 操作时，它对于多线程使用是线程安全的

## 扩展

当前`HashTable`中的被占用空间达到一个百分比的时候就将该空间自动扩容，在`.net`中这个百分比是72%.  
且初始大小为3

```C#
private const Int32 InitialSize = 3;
this.loadFactor = 0.72f * loadFactor;
```

然后比较用的是`0x7FFFFFFF`

```C#
if (((b.hash_coll &  0x7FFFFFFF) == hashcode) && 
			KeyEquals (b.key, key))
			return true;
```

.net中是通过探测法解决哈希冲突的，当通过散列值取得的位置Postion以及被占用的时候，就会增加一个位移x值判断下一个位置Postion+x是否被占用，如果仍然被占用就继续往下位移x判断Position+2*x位置是否被占用，如果没有被占用则将值放入其中。当HashTable中的可用空间越来越小时，则获取得到可用空间的难度越来越大，消耗的时间就越多

自动扩容的大小是当前空间大小的两倍最接近的素数

注意Add是`Object`类型的,值类型难免装箱拆箱

```C#
public virtual void Add(Object key, Object value) {
	Insert(key, value, true);
}
```

# Queue 

先进先出集合

> 不建议使用 `Queue` 类进行新的开发。 相反，我们建议使用泛型 `Queue<T>` 类

# ReadOnlyCollectionBase 

提供强类型化非泛型只读集合的 `abstract` 基类

# SortedList 

表示键/值对的集合，这些键值对按键排序并可按照键和索引访问

> 这些都是过时的, 自从泛型出来后

`System.Collections.Generic`这个空间下

# Stack

表示对象的简单后进先出 (LIFO) 非泛型集合

# StructuralComparisons 

提供用于执行两个集合对象的结构化比较的对象

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
