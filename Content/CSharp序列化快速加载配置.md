---
title: CSharp序列化快速加载配置
date: 2019-06-17 22:18:00
updated: 2019-06-17 22:18:00
categories:
	- C#
tags: 
	- C#
---
# CSharp序列化快速加载配置

这次不是搬运, 是我在公司做存储配置和解析配置的是否突然想到C#好像有序列化的东西.  
于是了解了一番.  

使用C#自带的序列化, 能让你不必编写繁琐的配置解析代码, 直接使用api将类存储为文件, 之后也可以反序列化将文件变为类. 主要用于数据的存储.

除了解析配置, 还有其他使用清空请自行探索

[参考](https://www.cnblogs.com/lsy131479/p/8371858.html "参考") 


<!--more-->

# 快速使用

导入命名空间
```
using System.Runtime.Serialization.Formatters.Binary;
```
声明控制器
```
BinaryFormatter bf = new BinaryFormatter();
```
序列化
```
//创建文件流派
Stream stream = new FileStream("save.bin", FileMode.Create);
//将对象或具有指定顶级 （根）、 对象图序列化到给定的流
bf.Serialize(stream, list);
//关闭流
stream.Close();
```
反序列化
```
//创建文件流派
Stream stream = new FileStream("save.bin", FileMode.Open);
//二进制序列化
BinaryFormatter bf = new BinaryFormatter();
//指定的流反序列化对象图
(你的类)bf.Deserialize(stream);
```

# 说明

using和加载控制器就没什么好说的了. 就是一个api类生命, 详情可以自己参阅文档.

然后就是参数, 这里其实有两个方法

`序列化`Serialize()实际上就是将你的类转化为一个文件.  
`反序列化`Deserialize就是将一个你之前序列化的文件转化为一个类.

> 这里需要注意, 如果你保存之后对类的文件有一些修改, 你可能还需要监听序列化事件进行自己的序列化操作. 很麻烦, 细节自己查询吧. 以后我用到了可能会补充一些.

虽然说反序列化只需要一个参数, 但实际上都等同, 都需要一个文件流和一个你需要序列化的类.

> 忘了说了, 被序列化的类需要支持`[Serializable]`这个特性, 而且需要递归支持, 这表示你不能用其他类库中的类来序列化, 除非他们天然支持序列化.

然后特别注意文件流, 序列化的时候需要Create. 读取则需要Open.

最后.

# 注意

在Unity编辑器下使用好像在刷新编译之后类中的数据会清空, 此时需要监听刷新事件. 重新反序列化读取.

[监听编译检测](https://blog.csdn.net/rhett_yuan/article/details/79615836)

我用第二种好一点.
