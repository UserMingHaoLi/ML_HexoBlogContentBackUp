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



<!--more-->

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
