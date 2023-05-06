---
title: CSharp类解析_Path
date: 2021-07-05 21:48:00
updated: 2021-07-05 21:48:00
id: ml-20230505-235390-g185
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

`System.IO.Path`   
其他引用  
`System.IO.PathInternal`

<!--more-->

# 预定义

主要是一些特殊符号  
`\\, /, :, ;, <, >, |, *, ?, \0, 数字` 

# 路径长度

```CSharp
//最大总路径为260，最大单个组件长度为255。
//例如，D:\<256个字符的文件名>是不合法的，即使它的字符数低于260个。
```
`MaxPath = PathInternal.MaxShortPath = 260;`  
`MaxDirectoryLength = PathInternal.MaxComponentLength = 255;`



# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
