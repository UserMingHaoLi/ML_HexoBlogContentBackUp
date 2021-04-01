---
title: Markdown进阶
date: 2019-06-05 22:19:00
updated: 2020-03-27 21:42:00
id: ml-20190605-221900-g4
categories:
	- 写作基础
tags: 
	- Markdown
---

[toc]

# Markdown进阶

今天就先移动这么多吧, 每次玩游戏, 都感觉到一种深深的负罪感. 长此以往我可能会有精神问题.

进阶篇的内容有写解释器支持有写不支持, 也不知道hexo怎么样. 看看吧.

内容大多数都是有道云笔记支持的.

> 内容来自 <http://note.youdao.com/iyoudao/?p=2411> 是Markdown进阶支持

<!--more-->


## 加色

使用`====`包裹内容

`==内容==`

==内容==

## 数学

相应的数学符号恐怕得百度.

    ```math
    E = mc^2
    ```

```math
E = mc^2
```

## 流程图

使用代码块, 之后是`graph`开头  
`LR`的意思是从`left`到`right`  
你也可以写`TB`,`BT`,`RL`

    ```
    graph LR
    A-->B
    ```

```
graph LR
A-->B
```

## 序列图

关键字`sequenceDiagram`

    sequenceDiagram
    A->>B: How are you?
    B->>A: Great!

```
sequenceDiagram
A->>B: How are you?
B->>A: Great!
```

## 甘特图

关键字`gantt`

    gantt
    dateFormat YYYY-MM-DD
    section S1
    T1: 2014-01-01, 9d
    section S2
    T2: 2014-01-11, 9d
    section S3
    T3: 2014-01-02, 9d

```
gantt
dateFormat YYYY-MM-DD
section S1
T1: 2014-01-01, 9d
section S2
T2: 2014-01-11, 9d
section S3
T3: 2014-01-02, 9d
```

## 目录

会自动依据标题符`#`来生成目录, 层级就是`#`的个数

```
[toc]
```

此处的目录就在开头

[Toc]

> 现在使用的Markdown插件一般都支持自动生成目录功能

## 脚注

在任何地方使用`[^*]`其中`*`是通配符, 可以是任何长度的字符  
之后再任何地方重新声明脚注`[^*]: 内容`  
脚注具有跳转功能

```
脚注[^j]

[^j]: 脚注
```

脚注[^j]


[^j]: 脚注

### 脚注注释

用一个不使用的脚注来隐藏具体信息  
达到注释的效果

```
[^z]: 这里是注释 你不知道
    就是看不到
    啊哈
```

然后看不到注释咯

[^z]: 这里是注释 你不知道
    就是看不到
    啊哈
    
[^_^]: 其实你开可以使用^开头的颜文字做标头

### 链接注释

用一个不使用的链接来隐藏具体信息  
达到注释的效果

```
[linkz]:
    /
    "
    这里书写注释内容
    随意换行
    但不能空行
    "
```

然后还是看不到注释咯

[linkz]:
    /
    "
    这里书写注释内容
    随意换行
    但不能空行
    "

[>_>]:
    /
    "
    使用一些颜文字描述心情
    "