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

# Add()

```C#
public virtual int Add(Object value) {
	Contract.Ensures(Contract.Result<int>() >= 0);
	if (_size == _items.Length) EnsureCapacity(_size + 1);
	_items[_size] = value;
	_version++;
	return _size++;
}
```
*注意`EnsureCapacity`,这是扩容*

# AddRange

```C#
InsertRange(_size, c);
```

# EnsureCapacity

使用指定的比较器在已排序 ArrayList 的某个元素范围中搜索元素，并返回该元素从零开始的索引

也就是搜索查找

```C#
Array.BinarySearch((Array)_items, index, count, value, comparer);
```
后面都是这些重载

# Clear()

```C#
if (_size > 0)
{
	Array.Clear(_items, 0, _size); 
	_size = 0;
}
_version++;
```

# Clone()

```C#
Contract.Ensures(Contract.Result<Object>() != null);
	ArrayList la = new ArrayList(_size);
	la._size = _size;
	la._version = _version;
	Array.Copy(_items, 0, la._items, 0, _size);
	return la;
```
就是`new`  

# Contains()

```C#
public virtual bool Contains(Object item) {
	if (item==null) {
		for(int i=0; i<_size; i++)
			if (_items[i]==null)
				return true;
		return false;
	}
	else {
		for(int i=0; i<_size; i++)
			if ( (_items[i] != null) && (_items[i].Equals(item)) )
				return true;
		return false;
	}
}
```
*这里没用`==`而是用了`Equals`,并为`null`作了单独处理*

# CopyTo

实际是` Array.Copy`

# EnsureCapacity

经典的*2扩容来了
```C#
private void EnsureCapacity(int min) {
	if (_items.Length < min) {
		int newCapacity = _items.Length == 0? _defaultCapacity: _items.Length * 2;
		// Allow the list to grow to maximum possible capacity (~2G elements) before encountering overflow.
		// Note that this check works even when _items.Length overflowed thanks to the (uint) cast
		if ((uint)newCapacity > Array.MaxArrayLength) newCapacity = Array.MaxArrayLength;
		if (newCapacity < min) newCapacity = min;
		Capacity = newCapacity;
	}
}
```
*当前容量比所需最少容量小时, `_items.Length * 2`*  
然后设置`Capacity`,上面我们讲到过,这就是`New`

**所以如果是大数量,则一定要手动指定,不然就一直*2,消耗是很大的**

# FixedSize

几个`static`方法  
返回具有固定大小的 `ArrayList` 包装

# GetEnumerator

```C#
public virtual IEnumerator GetEnumerator() {
	Contract.Ensures(Contract.Result<IEnumerator>() != null);
	return new ArrayListEnumeratorSimple(this);
}

public virtual IEnumerator GetEnumerator(int index, int count) {
	if (index < 0)
		throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	if (count < 0)
		throw new ArgumentOutOfRangeException("count", Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	if (_size - index < count)
		throw new ArgumentException(Environment.GetResourceString("Argument_InvalidOffLen"));
	Contract.Ensures(Contract.Result<IEnumerator>() != null);
	Contract.EndContractBlock();

	return new ArrayListEnumerator(this, index, count);
}
```

可以看到是`new`了一个内部类`ArrayListEnumerator`

```C#
private sealed class ArrayListEnumerator : IEnumerator, ICloneable
{
	private ArrayList list;
	private int index;
	private int endIndex;       // Where to stop.
	private int version;
	private Object currentElement;
	private int startIndex;     // Save this for Reset.

	internal ArrayListEnumerator(ArrayList list, int index, int count) {
		this.list = list;
		startIndex = index;
		this.index = index - 1;
		endIndex = this.index + count;  // last valid index
		version = list._version;
		currentElement = null;
	}

	public Object Clone() {
		return MemberwiseClone();
	}

	public bool MoveNext() {
		if (version != list._version) throw new InvalidOperationException(Environment.GetResourceString(ResId.InvalidOperation_EnumFailedVersion));
		if (index < endIndex) {
			currentElement = list[++index];
			return true;
		}
		else {
			index = endIndex + 1;
		}
		
		return false;
	}

	public Object Current {
		get {
			if (index < startIndex) 
				throw new InvalidOperationException(Environment.GetResourceString(ResId.InvalidOperation_EnumNotStarted));
			else if (index > endIndex) {
				throw new InvalidOperationException(Environment.GetResourceString(ResId.InvalidOperation_EnumEnded));
			}
			return currentElement;
		}
	}

	public void Reset() {
		if (version != list._version) throw new InvalidOperationException(Environment.GetResourceString(ResId.InvalidOperation_EnumFailedVersion));
		index = startIndex - 1;                
	}
}
```
*就是一个经典迭代器,没什么好说的*

# IndexOf

```C#
public virtual int IndexOf(Object value) {
		Contract.Ensures(Contract.Result<int>() < Count);
		return Array.IndexOf((Array)_items, value, 0, _size);
	}
```
*就调了下`Array.IndexOf`*

# Insert

```C#
public virtual void Insert(int index, Object value) {
		// Note that insertions at the end are legal.
		if (index < 0 || index > _size) throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_ArrayListInsert"));
		//Contract.Ensures(Count == Contract.OldValue(Count) + 1);
		Contract.EndContractBlock();

		if (_size == _items.Length) EnsureCapacity(_size + 1);
		if (index < _size) {
			Array.Copy(_items, index, _items, index + 1, _size - index);
		}
		_items[index] = value;
		_size++;
		_version++;
	}
```
*可以看出`Insert`还是比较耗的,用了`Array.Copy`*

# LastIndexOf

```C#
public virtual int LastIndexOf(Object value, int startIndex, int count) {
	if (Count != 0 && (startIndex < 0 || count < 0))
		throw new ArgumentOutOfRangeException((startIndex<0 ? "startIndex" : "count"), Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	Contract.Ensures(Contract.Result<int>() < Count);
	Contract.EndContractBlock();

	if (_size == 0)  // Special case for an empty list
		return -1;

	if (startIndex >= _size || count > startIndex + 1) 
		throw new ArgumentOutOfRangeException((startIndex>=_size ? "startIndex" : "count"), Environment.GetResourceString("ArgumentOutOfRange_BiggerThanCollection"));

	return Array.LastIndexOf((Array)_items, value, startIndex, count);
}
```
`Array.LastIndexOf`实际上也是遍历查找

# ReadOnly

返回只读的 ArrayList 包装  
同样没用过

# Remove

# RemoveAt

```C#
public virtual void Remove(Object obj) {
	Contract.Ensures(Count >= 0);

	int index = IndexOf(obj);
	BCLDebug.Correctness(index >= 0 || !(obj is Int32), "You passed an Int32 to Remove that wasn't in the ArrayList." + Environment.NewLine + "Did you mean RemoveAt?  int: "+obj+"  Count: "+Count);
	if (index >=0) 
		RemoveAt(index);
}
public virtual void RemoveAt(int index) {
	if (index < 0 || index >= _size) throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_Index"));
	Contract.Ensures(Count >= 0);
	//Contract.Ensures(Count == Contract.OldValue(Count) - 1);
	Contract.EndContractBlock();

	_size--;
	if (index < _size) {
		Array.Copy(_items, index + 1, _items, index, _size - index);
	}
	_items[_size] = null;
	_version++;
}
```

可见,和`insert`类似,都使用了Copy

# RemoveRange

```C#
public virtual void RemoveRange(int index, int count) {
	if (index < 0)
		throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	if (count < 0)
		throw new ArgumentOutOfRangeException("count", Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	if (_size - index < count)
		throw new ArgumentException(Environment.GetResourceString("Argument_InvalidOffLen"));
	Contract.Ensures(Count >= 0);
	//Contract.Ensures(Count == Contract.OldValue(Count) - count);
	Contract.EndContractBlock();

	if (count > 0) {
		int i = _size;
		_size -= count;
		if (index < _size) {
			Array.Copy(_items, index + count, _items, index, _size - index);
		}
		while (i > _size) _items[--i] = null;
		_version++;
	}
}
```
`Array.Copy`

# Repeat

```C#
ArrayList list = new ArrayList((count>_defaultCapacity)?count:_defaultCapacity);
for(int i=0; i<count; i++)
	list.Add(value);
return list;
```

返回 `ArrayList`，其元素是指定值的副本。

# Reverse

```C#
Array.Reverse(_items, index, count);
```

# SetRange

```C#
public virtual void SetRange(int index, ICollection c) {
	if (c==null) throw new ArgumentNullException("c", Environment.GetResourceString("ArgumentNull_Collection"));
	Contract.EndContractBlock();
	int count = c.Count;
	if (index < 0 || index > _size - count) throw new ArgumentOutOfRangeException("index", Environment.GetResourceString("ArgumentOutOfRange_Index"));
	
	if (count > 0) {
		c.CopyTo(_items, index);
		_version++;
	}
}
```
*使用`ICollection.CopyTo`*

# GetRange

```C#
public virtual ArrayList GetRange(int index, int count) {
	if (index < 0 || count < 0)
		throw new ArgumentOutOfRangeException((index<0 ? "index" : "count"), Environment.GetResourceString("ArgumentOutOfRange_NeedNonNegNum"));
	if (_size - index < count)
		throw new ArgumentException(Environment.GetResourceString("Argument_InvalidOffLen"));
	Contract.Ensures(Contract.Result<ArrayList>() != null);
	Contract.EndContractBlock();
	return new Range(this,index, count);
}
```
*可以看到`new Range`*  
*这是`private class Range: ArrayList`*

#  Sort

` Array.Sort`

# Synchronized

Synchronized(ArrayList)	
返回同步的（线程安全）ArrayList 包装器。
Synchronized(IList)	
返回同步的（线程安全）IList 包装器。

# ToArray

```C#
public virtual Object[] ToArray() {
	Contract.Ensures(Contract.Result<Object[]>() != null);

	Object[] array = new Object[_size];
	Array.Copy(_items, 0, array, 0, _size);
	return array;
}
```
*还是`new Object` + `Array.Copy`*

# IListWrapper

` private class IListWrapper : ArrayList`

内部类型,用于`Adapter`的返回值

## IListWrapperEnumWrapper

用于提供`public override IEnumerator GetEnumerator`

# SyncArrayList

`private class SyncArrayList : ArrayList`

用于`Synchronized(ArrayList list) `

> 地下的一些类都是这种包装用途

# 总结

可以看到,基本都是`Array`也就是`数组`的包装.  
而且一旦涉及容量变化和副本,则必定`new Object`.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
