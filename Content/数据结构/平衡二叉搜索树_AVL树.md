---
title: 平衡二叉搜索树_AVL树
date: 2021-09-01 00:47:00
updated: 2021-09-01 00:47:00
id: ml-20210901-004700-g156
categories:
	- 数据结构
tags: 
	- 数据结构
---

对于一颗二叉树,令其每个节点(包括根节点)  
左边的值小,右边的值大

> 对于相同的值,类似于<Key,Value>. 直接覆盖,不生成新节点

如果需要查找一个值,那么只需要不断比较当前节点  
比当前节点大就向右, 小就向左, 相同就匹配返回.

> 看得出来,实际上就是一个经典的二分查找,每次可以排除当前一半的数据

*可见,查询次数与树的深度相关, 而树的存储量随着深度成指数上升, 所以查找效率很高*

定义深度为n,则容量为`2^(n+1)-1`,定义此值为可容纳节点数m  
倒过来就是`log2(m+1)-1 = n`  
*时间复杂度为`O(log(m))`*

> 实际上,平衡二叉树的定义为`任何节点的左子树和右子树的高度差不超过1`

> `AVL`则是自平衡的意思,也就是在插入或者删除的时候,重新变化树来使其平衡.

<!--more-->


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
