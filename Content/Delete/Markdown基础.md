---
title: Markdown基础
date: 2019-06-05 22:17:00
updated: 2020-03-27 21:25:00
id: ml-20190605-221700-g21
categories:
	- Delete
tags: 
	- Delete
---

[toc]

# Markdown基础

终于决定要吧有道笔记的内容同步到博客, 希望我能定期备份吧.

上次双十一买了云服务器, 本来是准备搭建博客的, 也确实搭建成功了, 但是最终还是使用有道云笔记来做笔记. 现在想通了, 还是移动到博客上来吧.

> 内容来自 <http://www.markdown.cn> 是Markdown原生支持的最基础

<!--more-->

> PS: GitHub使用的语法是 `GitHub-Flavored-Markdown`

## 标题

使用 `#` 号来表示一个大号加粗的标题, 连续的 `#` 最多支持6个, 约多越小

    # 你好
    ## 你好
    ### 你好
    #### 你好
    ##### 你好
    ###### 你好

>以上文字转化后就是下面这样的

# 你好
## 你好
### 你好
#### 你好
##### 你好
###### 你好

>注意, 标题在一般的编辑器中是有目录作用的.
>如果你要加粗, 建议使用 一对 \*\* 来包裹内容  
像是这样 **内容**

## 引用

### 单行引用

再一行的开头使用 `>` 可以使他看起来像是一行引用.

    >本文来自 http://www.markdown.cn
    
*转化后就是*
    
>本文来自 http://www.markdown.cn

### 多行引用

当然, 连续的使用`>`, 那就会产生区块引用, 或者说多行引用

#### 分散的多行引用

    >本文来自 http://www.markdown.cn
    
    >如果你喜欢, 记得帮我们推广
    
    >谢谢
    
    
*如果你每行有一个空行, 那么, 就像下面这样*
    
>本文来自 http://www.markdown.cn

>如果你喜欢, 记得帮我们推广

>谢谢

#### 集合的多行引用

    >本文来自 http://www.markdown.cn  
    >如果你喜欢, 记得帮我们推广  
    >谢谢  

*如果你没有空行, 只是在每行的末尾使用两个空格, 如下*

>本文来自 http://www.markdown.cn  
>如果你喜欢, 记得帮我们推广  
>谢谢  

#### 懒惰的多行引用

    >本文来自 http://www.markdown.cn  
    如果你喜欢, 记得帮我们推广  
    谢谢  
    
*如果你使用两个空格的方式, markdown允许你只在头部使用`>`, 效果和全部使用并无差异*
    
>本文来自 http://www.markdown.cn  
如果你喜欢, 记得帮我们推广  
谢谢  

#### 嵌套引用

    >本文来自 http://www.markdown.cn  
    >>如果你喜欢, 记得帮我们推广  
    >>>谢谢 
    >>最后  //此处没有回归第二层
    >
    >感谢   //使用空行引用, 回到第一层
    
*使用多个箭头来表示你的引用关系层级, 如果你希望回撤引用层级, 你需要一个引用空行*
    
>本文来自 http://www.markdown.cn  
>>如果你喜欢, 记得帮我们推广  
>>>谢谢  
>>最后  //此处没有回归第二层
>
>感谢   //使用空行引用, 回到第一层

#### 引用中的Markdown语法

    > # 本文来自 http://www.markdown.cn
    >>*如果你喜欢, 记得帮我们推广*
    >>>* 谢谢 

*在引用中一样可以使用语法*

> # 本文来自 http://www.markdown.cn
>>*如果你喜欢, 记得帮我们推广*
>>>* 谢谢 

## 列表

Markdown 支持有序列表和无序列表.

### 无序列表

无序列表使用`*`、`+`或是`-`作为列表标记:

    * 第一项
    + 第二项
    - 第三项
    - 第四项

> 你可以从项的间隔中发现, 使用不同的符号, 不是同一个列表.  
第一项和第二项都是自己单独成列表, 第三项和第四项则是一个列表.

* 第一项
+ 第二项
- 第三项
- 第四项

### 有序列表

使用数字和`.`号来表示一个项

#### 顺序版本

    1. 第一项
    2. 第二项
    3. 第三项

1. 第一项
2. 第二项
3. 第三项

#### 非顺序版本

    1. 第一项
    3. 第二项
    2. 第三项
    99. 第九十九项

> 可以看出, 其实所谓的有序列表只是markdown显示排序, 和你自己标号的数组并没有关系.

1. 第一项
3. 第二项
2. 第三项
99. 第九十九项

### 懒惰列表

    * 你好  
    我想你
    * 不  
    你不想

> 你可以在一行的开头使用`*`, 之后使用两个空格,在换行.

* 你好  
我想你
* 不  
你不想



    1. 你好  
    我想你
    2. 不  
    你不想

> 类似的, 有序列表也可以这样

1. 你好  
我想你
2. 不  
你不想

> **注意**, 如果你一定要书写一个数字后加上`.`然后再空格.  
> 例如  
> 1986\. What a great season.  
> 希望你能加上转义字符 `1986\. What a great season.`

## 代码区块

其实我更喜欢称呼它为**原样显示区块**  
只需要在任何行后面再次缩进一个`tab`即可

    下面我们演示代码区块
        
        这里就是代码区块了
        它可以有多行
        
下面我们演示代码区块
    
    这里就是代码区块了
    它可以有多行
    
> 注意, 处于这个区块的代码,  Markdown 语法不会被转换

## 分隔线

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线,行内不能有其他东西.你也可以在星号或是减号中间插入空格.下面每种写法都可以建立分隔线:

    * * *
    ***
    *****
    - - -
    ---------------------------------------
    
* * *
***
*****
- - -
---------------------------------------


> 那么基础部分完毕, 欲知后事如何,且看`高级篇`和`进阶篇`.  
