---
title: CSharp类解析_ArrayList
date: 2021-07-05 21:48:00
updated: 2021-07-05 21:48:00
id: ml-20210705-214800-g146
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

# 前言

实现  
`ICollection`  `IEnumerable`  `IList`  `ICloneable`

可知他是一个可迭代的元素集合也就是`List`

> 官方建议使用`List<T>`, 说这个性能不好, 毕竟有类型强转和装箱拆箱

*仅 `.NET Framework`： 对于非常大的 `ArrayList` 对象，可以通过 `enabled` `<gcAllowVeryLargeObjects>` `true` 在运行时环境中将配置元素的属性设置为，来将64位系统上的最大容量增加到`2000000000`个元素。*

可以使用整数索引访问此集合中的元素。 此集合中的索引从零开始。

接受 null 为有效的值。 还允许重复元素

不支持使用多维数组作为集合中的元素

<!--more-->

# 构造函数

首先是一串经典定义

```C#
private Object[] _items;
[ContractPublicPropertyName("Count")]
private int _size;
private int _version;
[NonSerialized]
private Object _syncRoot;

private const int _defaultCapacity = 4;
private static readonly Object[] emptyArray = EmptyArray<Object>.Value; 


```
*如此,我们可知其内部实现为`Object[]`, 且默认有`4`的长度*  

顺便,注意这个`_version`,他是用来维护版本号的, 再迭代器的`MoveNext()`和`Reset()`时候,版本号不一致会报错
```C#
 if (version != list._version) throw new InvalidOperationException(Environment.GetResourceString(ResId.InvalidOperation_EnumFailedVersion));
```

之后就是构造函数,没什么好说的,就是给了个空组,也就是还没给Count
```C#
public ArrayList() {
	_items = emptyArray;  
}
```

顺带一提,`emptyArray`长这样
```C#
internal static class EmptyArray<T>
{
    public static readonly T[] Value = new T[0];
}
```

当然,还有另一种指定长度的构造函数
```C#
public ArrayList(int capacity) {
	if (capacity < 0) throw new ArgumentOutOfRangeException("capacity", Environment.GetResourceString("ArgumentOutOfRange_MustBeNonNegNum", "capacity"));
	Contract.EndContractBlock();

	if (capacity == 0)
		_items = emptyArray;
	else
		_items = new Object[capacity];
}
```
*参数不小于0, 之后 `new Object[capacity]`*

然后是以`ICollection`参数创建
```C#
public ArrayList(ICollection c) {
	if (c==null)
		throw new ArgumentNullException("c", Environment.GetResourceString("ArgumentNull_Collection"));
	Contract.EndContractBlock();

	int count = c.Count;
	if (count == 0)
	{
		_items = emptyArray;
	}
	else {
		_items = new Object[count];
		AddRange(c);
	}
}
```
*参数不为`null`然后也是`new Object[count]`,再`AddRange(c)`*

# Capacity

实际长度,也就是`_items.Length`

```C#
public virtual int Capacity {
get {
	Contract.Ensures(Contract.Result<int>() >= Count);
	return _items.Length;
}
set {
	if (value < _size) {
		throw new ArgumentOutOfRangeException("value", Environment.GetResourceString("ArgumentOutOfRange_SmallCapacity"));
	}
	Contract.Ensures(Capacity >= 0);
	Contract.EndContractBlock();
	// We don't want to update the version number when we change the capacity.
	// Some existing applications have dependency on this.
	if (value != _items.Length) {
		if (value > 0) {
			Object[] newItems = new Object[value];
			if (_size > 0) { 
				Array.Copy(_items, 0, newItems, 0, _size);
			}
			_items = newItems;
		}
		else {
			_items = new Object[_defaultCapacity];
		}
	}            
}
```
*然后`Set`的时候又是重写`new Object[value]`然后进行`Array.Copy()`*  
所以可想而知性能不是很好,这就是可变长度的代价.

# Count

也就是外界看到的长度, 实际上是已有元素的数量.
```C#
public virtual int Count {
	get {
		Contract.Ensures(Contract.Result<int>() >= 0);
		return _size;
	}
}
```

# IsFixedSize

# IsReadOnly

# IsSynchronized

三个都是`return false`不支持

# SyncRoot

这个锁倒是有,是在前面定义,用的时候生成
```C#
public virtual Object SyncRoot {
	get { 
		if( _syncRoot == null) {
			System.Threading.Interlocked.CompareExchange<Object>(ref _syncRoot, new Object(), null);    
		}
		return _syncRoot; 
	}
}
```

# this[int index]

```C#
public virtual Object this[int index] {
	get {
		if (index < 0 || index >= _size) throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_Index"));
		Contract.EndContractBlock();
		return _items[index];
	}
	set {
		if (index < 0 || index >= _size) throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_Index"));
		Contract.EndContractBlock();
		_items[index] = value;
		_version++;
	}
}
```
*看到`_version++`了码,`Set`操作都会做这个,`Get`则不会*

# Adapter

为特定 IList 创建 ArrayList 包装。   
`static`的, 没用过.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
