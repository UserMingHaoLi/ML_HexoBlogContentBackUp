---
title: AssetBundle的使用大全
date: 2023-04-10 22:04:00
updated: 2023-04-10 22:04:00
id: ml-20230410-220400-g184
categories:
	- Unity
	- AssetBundle
tags: 
	- Unity
	- AssetBundle
---

![AssetBundle学习路线图]

<!--more-->

# Direct References(直接引用-挂链接)

继承自`MonoBehaviour`实际上也继承自`Component`  
所以他们是组件, 脚本中的值可在 `Inspector` 中编辑

如果变量名称符合一组规则之一,则检查器显示变量名称的方式可能与您在脚本中定义变量名称的方式不同:

从开头删除"m_"
从开头删除"k"
从开头删除"_"
将第一个字母大写
在小写和大写字符之间添加空格
在下一个单词的开头的首字母缩略词和大写字符之间添加一个空格
在某些特殊情况下,例如"iPad"或"x64",这些规则不适用。

# Resources Folders(Resources文件夹)

# Asynchronous Loading From Resources(从Resources文件夹异步加载)

# Asset Bundles

# Bundle Dependency Loading(依赖项加载)

# AssetBundle Layout Strategy(资源组合打包策略)

# Bundle Manifest(Manifest文件清单)

# Duplicate vs.shared Assets(共享资源管理)

# Memory Management(内存管理)

# ShaderVariantsCollect(shader变体收集)

# ~~Addressable(可寻址资源)~~

待续

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
[AssetBundle学习路线图]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/AssetBundle%E4%BD%BF%E7%94%A8%E5%A4%A7%E5%85%A8/AssetBundle%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF.png?raw=true "AssetBundle学习路线图"
<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
