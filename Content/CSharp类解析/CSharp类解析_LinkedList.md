---
title: CSharp类解析_LinkedList
date: 2021-10-24 20:08:00
updated: 2021-10-24 20:08:00
id: ml-20211024-200800-g161
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

# 前言

链表

<!--more-->

# 首先是定义

```C#
internal LinkedListNode<T> head;
internal int count;
internal int version;
private Object _syncRoot;
```
*基本的,表头,数量,版本号*

```C#
public int Count {
	get { return count;}
}

public LinkedListNode<T> First {
	get { return head;}
}

public LinkedListNode<T> Last {
	get { return head == null? null: head.prev;}
}

bool ICollection<T>.IsReadOnly {
	get { return false; }
}
```
*可见表头的`prev`就是表尾,还是个环状链表*

# 构造函数

```C#
public LinkedList() {
}

public LinkedList(IEnumerable<T> collection) {
	if (collection==null) {
		throw new ArgumentNullException("collection");
	}

	foreach( T item in collection) {
		AddLast(item);
	}
}
#if !SILVERLIGHT
protected LinkedList(SerializationInfo info, StreamingContext context) {
	siInfo = info;
}
#endif
```
*空构造啥也没做,`IEnumerable<T>`构造调用了`AddLast(item)`,还有一个被宏夹住的`SerializationInfo`序列化构造*

# Add

```C#
void ICollection<T>.Add(T value) {
	AddLast(value);
}
```
*就是调了个` AddLast(value)`

```C#
public LinkedListNode<T> AddAfter(LinkedListNode<T> node, T value) {
	ValidateNode(node);
	LinkedListNode<T> result = new LinkedListNode<T>(node.list, value); 
	InternalInsertNodeBefore(node.next, result);
	return result;
}

public void AddAfter(LinkedListNode<T> node, LinkedListNode<T> newNode) {
	ValidateNode(node);
	ValidateNewNode(newNode);
	InternalInsertNodeBefore(node.next, newNode);
	newNode.list = this;
}    

public LinkedListNode<T> AddBefore(LinkedListNode<T> node, T value) {
	ValidateNode(node);    
	LinkedListNode<T> result = new LinkedListNode<T>(node.list, value);
	InternalInsertNodeBefore(node, result);
	if ( node == head) {
		head = result;
	}
	return result;
}    

public void AddBefore(LinkedListNode<T> node, LinkedListNode<T> newNode) {
	ValidateNode(node);    
	ValidateNewNode(newNode);                        
	InternalInsertNodeBefore(node, newNode);
	newNode.list = this;
	if ( node == head) {
		head = newNode;
	}
}    

public LinkedListNode<T> AddFirst(T value) {
	LinkedListNode<T> result = new LinkedListNode<T>(this, value);
	if (head == null) {
		InternalInsertNodeToEmptyList(result);
	} 
	else {
		InternalInsertNodeBefore( head, result);               
		head = result;                
	}
	return result;
}

public void AddFirst(LinkedListNode<T> node) {
	ValidateNewNode(node);

	if (head == null) {
		InternalInsertNodeToEmptyList(node);
	} 
	else {
		InternalInsertNodeBefore( head, node); 
		head = node;                
	}
	node.list = this;
}

public LinkedListNode<T> AddLast(T value) {
	LinkedListNode<T> result = new LinkedListNode<T>(this, value);        
	if (head== null) {
		InternalInsertNodeToEmptyList(result);
	} 
	else {
		InternalInsertNodeBefore( head, result);               
	}       
	return result;
}

public void AddLast(LinkedListNode<T> node) {
	ValidateNewNode(node);            

	if (head == null) {
		InternalInsertNodeToEmptyList(node);
	} 
	else {
		InternalInsertNodeBefore( head, node); 
	}
	node.list = this;
}
```
*这些Add没啥内涵,主要涉及`ValidateNode`,`ValidateNewNode`,`InternalInsertNodeBefore`,`InternalInsertNodeToEmptyList`*

注意`AddBefore`的时候要考虑变更表头.

# Clear

```C#
public void Clear() {
	LinkedListNode<T> current = head;             
	while (current != null ) {
		LinkedListNode<T> temp = current;
		current = current.Next;   // use Next the instead of "next", otherwise it will loop forever
		temp.Invalidate();                
	}

	head = null;
	count = 0;             
	version++;     
}
```
*逐个`Next`并清理,最后将`head`和`count`处理掉*

# CopyTo

```C#
public void CopyTo( T[] array, int index) {
	if (array == null) {
		throw new ArgumentNullException("array");
	}

	if(index < 0 || index > array.Length) {
		throw new ArgumentOutOfRangeException("index",SR.GetString(SR.IndexOutOfRange, index) );                    
	}

	if (array.Length - index < Count) {
		throw new ArgumentException(SR.GetString(SR.Arg_InsufficientSpace));
	}

	LinkedListNode<T> node = head;
	if (node != null) {
		do {
			array[index++] = node.item;                             
			node = node.next;
		} while (node != head);
	}
}
```
*将表中内容Copy到Array中*

# Contains 和 Find

```C#
public bool Contains(T value) {
	return Find(value) != null;    
}
public LinkedListNode<T> Find(T value) {
	LinkedListNode<T> node = head;
	EqualityComparer<T> c = EqualityComparer<T>.Default;            
	if (node != null) {
		if (value != null) {
			do {
				if (c.Equals(node.item, value)) {
					return node;   
				}
				node = node.next;
			} while (node != head);
		} 
		else {
			do {
				if (node.item == null) {
					return node;
				}
				node = node.next;
			} while (node != head);
		}
	}
	return null;        
}
```
*`FindLast`就是从尾巴开始(因为是环,尾巴就是头的`prev`),不赘述了*  
*可以看到`Contains`就是调用了`Find`*  
然后`Find`中有对`null`的额外判断,如果链表本身为null,则返回null,如果是要查找null,那么返回一个data为null的节点(节点本身不为null)

> 注意,这里使用了`EqualityComparer<T>.Default`作为比较器.

这个比较器长成这样
```C#
public override bool Equals(T x, T y) {
	if (x != null) {
		if (y != null) return x.Equals(y);
		return false;
	}
	if (y != null) return false;
	return true;
}
```

# GetEnumerator

*经典两件套,内部类就不重复展示了*
```C#
public Enumerator GetEnumerator() {
	return new Enumerator(this);
}

IEnumerator<T> IEnumerable<T>.GetEnumerator() {
	return GetEnumerator();
}
```

# Remove

```C#
public bool Remove(T value) {
	LinkedListNode<T> node = Find(value);
	if (node != null) {
		InternalRemoveNode(node); 
		return true;
	}
	return false;
}

public void Remove(LinkedListNode<T> node) {
	ValidateNode(node);          
	InternalRemoveNode(node); 
}
			
public void RemoveFirst() {
	if ( head == null) { throw new InvalidOperationException(SR.GetString(SR.LinkedListEmpty)); }
	InternalRemoveNode(head);             
}

public void RemoveLast() {
	if ( head == null) { throw new InvalidOperationException(SR.GetString(SR.LinkedListEmpty)); }
	InternalRemoveNode(head.prev);     
}
```
*可见都是调用了`InternalRemoveNode`*

> 后续有两个序列化宏包裹的函数`GetObjectData`,`OnDeserialization` 就不展示了

# Internal函数

终于到数据操作部分了.

```C#
private void InternalInsertNodeBefore(LinkedListNode<T> node, LinkedListNode<T> newNode) {
	newNode.next = node;
	newNode.prev = node.prev;
	node.prev.next = newNode;
	node.prev = newNode;            
	version++;
	count++;
}//将newNode插入到node之前

private void InternalInsertNodeToEmptyList(LinkedListNode<T> newNode) {
	Debug.Assert( head == null && count == 0, "LinkedList must be empty when this method is called!");
	newNode.next = newNode;
	newNode.prev = newNode;
	head = newNode;
	version++;
	count++; 
}//只有一个节点且自环

internal void InternalRemoveNode(LinkedListNode<T> node) {
	Debug.Assert( node.list == this, "Deleting the node from another list!");   
	Debug.Assert( head != null, "This method shouldn't be called on empty list!");
	if ( node.next == node) {
		Debug.Assert(count == 1 && head == node, "this should only be true for a list with only one node");
		head  = null;
	} 
	else {
		node.next.prev = node.prev;
		node.prev.next = node.next;
		if ( head == node) {
			head = node.next;
		}
	}
	node.Invalidate();  
	count--;
	version++;          
}//特殊处理了自环,表头
```

# Validate

```C#
internal void ValidateNewNode(LinkedListNode<T> node) {
	if (node == null) {
		throw new ArgumentNullException("node");                
	}

	if ( node.list != null) {
		throw new InvalidOperationException(SR.GetString(SR.LinkedListNodeIsAttached));                
	}
}//保证有数据,且node不属于任何链表(新节点)


internal void ValidateNode(LinkedListNode<T> node) {
	if (node == null) {
		throw new ArgumentNullException("node");                
	}

	if ( node.list != this) {
		throw new InvalidOperationException(SR.GetString(SR.ExternalLinkedListNode));
	}
}//保证有数据,且node属于自己
```

> 经典两件套`IsSynchronized`=false, `SyncRoot`现用现New

# ICollection.CopyTo

```C#
void System.Collections.ICollection.CopyTo(Array  array, int index) {
	if (array == null) {
		throw new ArgumentNullException("array");
	}

	if (array.Rank != 1) {
		throw new ArgumentException(SR.GetString(SR.Arg_MultiRank));
	}

	if( array.GetLowerBound(0) != 0 ) {
		throw new ArgumentException(SR.GetString(SR.Arg_NonZeroLowerBound));
	}

	if (index < 0) {
		throw new ArgumentOutOfRangeException("index",SR.GetString(SR.IndexOutOfRange, index) );
	}

	if (array.Length - index < Count) {
		throw new ArgumentException(SR.GetString(SR.Arg_InsufficientSpace));
	}

	T[] tArray = array as T[];
	if (tArray != null) {
		CopyTo(tArray, index);
	} 
	else {
		//
		// Catch the obvious case assignment will fail.
		// We can found all possible problems by doing the check though.
		// For example, if the element type of the Array is derived from T,
		// we can't figure out if we can successfully copy the element beforehand.
		//
		Type targetType = array.GetType().GetElementType(); 
		Type sourceType = typeof(T);
		if(!(targetType.IsAssignableFrom(sourceType) || sourceType.IsAssignableFrom(targetType))) {
			throw new ArgumentException(SR.GetString(SR.Invalid_Array_Type));                
		}

		object[] objects = array as object[];
		if (objects == null) {
			throw new ArgumentException(SR.GetString(SR.Invalid_Array_Type));
		}
			LinkedListNode<T> node = head;
		try {
			if (node != null) {
				do {
					objects[index++] = node.item;
					node = node.next;
				} while (node != head);
			}
		} 
		catch(ArrayTypeMismatchException) {
			throw new ArgumentException(SR.GetString(SR.Invalid_Array_Type));
		}
	}
}
```
对于能否强制转化为`T[]`有特殊处理,不然就强转为`object[]`

# LinkedListNode

*每个节点*
```C#
[System.Runtime.InteropServices.ComVisible(false)] 
public sealed class LinkedListNode<T> {
	internal LinkedList<T> list;
	internal LinkedListNode<T> next;
	internal LinkedListNode<T> prev;
	internal T item;
	
	public LinkedListNode( T value) {
		this.item = value;
	}

	internal LinkedListNode(LinkedList<T> list, T value) {
		this.list = list;
		this.item = value;
	}

	public LinkedList<T> List {
		get { return list;}
	}

	public LinkedListNode<T> Next {
		get { return next == null || next == list.head? null: next;}
	}

	public LinkedListNode<T> Previous {
		get { return prev == null || this == list.head? null: prev;}
	}

	public T Value {
		get { return item;}
		set { item = value;}
	}

	internal void Invalidate() {
		list = null;
		next = null;
		prev = null;
	}           
}   
```
*可以看到是双向的*

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
