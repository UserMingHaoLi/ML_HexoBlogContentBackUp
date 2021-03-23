---
title: Markdown总汇
date: 2021-03-12 23:30:00
updated: 2021-03-14 19:11:00
id: ml-20210312-233000-g3
categories:
	- 写作基础
tags: 
	- Markdown
---

规整并删除了其他Markdown文章,都集合到这篇来了  
以后所有MarkDown都在此更新

<!--more-->

- [在VSCode上编写Markdown](#在vscode上编写markdown)
  - [准备](#准备)
  - [插件](#插件)
    - [其他插件](#其他插件)
- [GitHub上的MarkDown语法](#github上的markdown语法)
  - [标题](#标题)
- [你好](#你好)
  - [你好](#你好-1)
    - [你好](#你好-2)
      - [你好](#你好-3)
        - [你好](#你好-4)
          - [你好](#你好-5)
  - [样式文本](#样式文本)
  - [引用文本](#引用文本)
    - [单行引用](#单行引用)
    - [多行引用](#多行引用)
      - [分散的多行引用](#分散的多行引用)
      - [集合的多行引用](#集合的多行引用)
      - [懒惰的多行引用](#懒惰的多行引用)
      - [嵌套引用](#嵌套引用)
      - [引用中的Markdown语法](#引用中的markdown语法)
  - [列表](#列表)
    - [无序列表](#无序列表)
    - [有序列表](#有序列表)
      - [顺序版本](#顺序版本)
      - [非顺序版本](#非顺序版本)
    - [懒惰列表](#懒惰列表)
  - [代码区块](#代码区块)
    - [嵌入在文字中的代码](#嵌入在文字中的代码)
    - [一整块代码](#一整块代码)
    - [一整块代码(带有颜色)](#一整块代码带有颜色)
  - [分隔线](#分隔线)
  - [链接](#链接)
    - [超链接](#超链接)
    - [快速链接](#快速链接)
    - [图片链接](#图片链接)
  - [复选框](#复选框)
  - [html代码](#html代码)
  - [表格](#表格)
  - [注释](#注释)
- [其他非GitHub支持的MarkDown内容](#其他非github支持的markdown内容)
- [拼写提示](#拼写提示)
- [完毕](#完毕)

# 在VSCode上编写Markdown

## 准备

首先肯定是需要准备一个[VSCode][VSCode_Link]

## 插件

> 以下内容使用前最好都查阅其文档

1. [中文（简体）语言包][vscode-language-pack-zh-hans_Link]  
2. Markdown编辑器 [Markdown All in One][markdown-all-in-one_Link]  
3. 快速黏贴图片到文章中 [Paste Image][vscode-paste-image_Link]  

### 其他插件

有其他需求的还可与继续选择以下插件

*后续还可能会不断更新*

1. 输出Markdown导出为`PDF`,`PNG`,`HTML` [Markdown Preview Enhanced](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced) 这个插件有专门的[中文文档](https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/)
2. 快速发布文章到[limfx.pro](https://www.limfx.pro/)而且还具备数学公式环境的插件 [LimfxCodeEx](https://marketplace.visualstudio.com/items?itemName=Limfx-Lee.limfxcodeex)  
    *. 注意`LimfxCodeEx`还需要依赖其他插件,具体请自行查阅文档

**此时就完成了全部的环境搭建工作了**

# GitHub上的MarkDown语法

> GitHub上通用的Markdown规范, 称其为 `GitHub Flavored Markdown`  

> 实际上Markdown只负责写,实际上不知道怎么渲染  
> 所以在不同的网站上有不同的渲染规则,得出的展示界面也不一样  

> 本文基于 [GitHub Docs 写作文档][github.docs.writing]  
> 更基础的Markdown规范可以前往 [markdown.cn][markdown.cn] 查阅原生支持  
> 更多示例请参阅 [GitHub Flavored Markdown 规范][gfm.example]

## 标题

使用 `#` 号来表示一个大号加粗的标题, 连续的 `#` 最多支持6个, 约多越小

    # 你好
    ## 你好
    ### 你好
    #### 你好
    ##### 你好
    ###### 你好

*以上文字转化后就是下面这样的*

# 你好
## 你好
### 你好
#### 你好
##### 你好
###### 你好

> 注意, 标题在一般的编辑器中是有目录作用的.  


## 样式文本

> 标题中也可以使用样式文本,事实上,他们几乎可以在Markdown影响的任何地方使用.但无法在代码块和HTML块中使用

有时候可能需要 **粗体** *斜体* ~~删除线~~ 来表示一些其他语义

| 样式             | 语法               | 可能的快捷键 | 示例                         | 输出                       |
| ---------------- | ------------------ | ------------ | ---------------------------- | -------------------------- |
| 粗体             | `** **`或者`__ __` | Ctrl + B     | `**这是粗体**`               | **这是粗体**               |
| 斜体             | `* *`或者`_ _`     | Ctrl + i     | `*这是斜体*`                 | *这是斜体*                 |
| 删除线           | `~~ ~~`            |              | `~~这是删除线~~`             | ~~这是删除线~~             |
| 粗体和嵌入的斜体 | `** **` 和 `_ _`   |              | `**此文本 _非常_ 重要**`     | **此文本 _非常_ 重要**     |
| 全部粗体和斜体   | `*** ***`          |              | `***所有这些文本都很重要***` | ***所有这些文本都很重要*** |


## 引用文本

### 单行引用

在一行的开头使用 `>` 可以使他看起来像是一行引用.

    >本文来自 ML-Blog
    
*转化后就是*
    
>本文来自 ML-Blog

### 多行引用

当然, 连续的使用`>`, 那就会产生区块引用, 或者说多行引用

#### 分散的多行引用

    >本文来自 ML-Blog
    
    >如果你喜欢, 记得帮我推广
    
    >谢谢
    
    
*如果你每行有一个空行, 那么, 就像下面这样*
    
>本文来自 ML-Blog

>如果你喜欢, 记得帮我推广

>谢谢

#### 集合的多行引用

    >本文来自 ML-Blog  
    >如果你喜欢, 记得帮我推广  
    >谢谢  

*如果你没有空行, 只是在每行的末尾使用两个空格, 如下*

>本文来自 ML-Blog  
>如果你喜欢, 记得帮我推广  
>谢谢  

#### 懒惰的多行引用

> 不推荐此写法

    >本文来自 ML-Blog  
    如果你喜欢, 记得帮我推广  
    谢谢  
    
*如果你使用两个空格的方式, markdown允许你只在头部使用`>`, 效果和全部使用并无差异*
    
>本文来自 ML-Blog  
如果你喜欢, 记得帮我推广  
谢谢  

>> 注意: 你可以认为两个空格代表小间隔,回车代表大间隔.这在大多数Markdown格式中都适用.

#### 嵌套引用

    >本文来自 ML-Blog  
    >>如果你喜欢, 记得帮我推广  
    >>>谢谢 
    >>最后  //此处没有回归第二层
    >
    >感谢   //使用空行引用, 回到第一层
    
*使用多个箭头来表示你的引用关系层级, 如果你希望回撤引用层级, 你需要一个引用空行*
    
>本文来自 ML-Blog  
>>如果你喜欢, 记得帮我推广  
>>>谢谢  
>>最后  //此处没有回归第二层
>
>感谢   //使用空行引用, 回到第一层

#### 引用中的Markdown语法

    > # 本文来自 ML-Blog
    >>*如果你喜欢, 记得帮我推广*
    >>>* 谢谢 

*在引用中一样可以使用语法*

> # 本文来自 ML-Blog
>>*如果你喜欢, 记得帮我推广*
>>>* 谢谢 

## 列表

Markdown 支持有序列表和无序列表。

### 无序列表

无序列表使用`*`、`+`或是`-`作为列表标记：

    * 第一项
    + 第二项
    - 第三项
    - 第四项

*你可以从项的间隔中发现, 使用不同的符号, 不是同一个列表*  
*第一项和第二项都是自己单独成列表, 第三项和第四项则是一个列表*

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

*可以看出, 其实所谓的有序列表只是markdown显示排序, 和你自己标号的数组并没有关系*

1. 第一项
2. 第二项
3. 第三项
4.  第九十九项

### 懒惰列表

> 不推荐此写法

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

1986\. What a great season.  
这样就不会被识别为列表, `.`也能正常显示

> GitHub还有一套缩进规则,详情请看[gfm-example-265](https://github.github.com/gfm/#example-265)

## 代码区块

其实我更喜欢称呼它为**原样显示区块**  

### 嵌入在文字中的代码

使用一对` `` `可标注句子中的代码或命令。 反引号中的文本**不会被格式化**。  
而且在大多数的Markdown渲染器中,还会被特殊颜色标记, 达到`醒目`的效果

*例如上面那段话中的醒目*

```
使用一对` `` `可标注句子中的代码或命令。 反引号中的文本**不会被格式化**。  
而且在大多数的Markdown渲染器中,还会被特殊颜色标记, 达到`醒目`的效果
```

### 一整块代码

在任何两个` ``` `之间的代码都**不会被格式化**

> 也可以在任何行后面再次缩进一个`tab`也就是`\t`,又称制表符.表示进入代码区块,但是不推荐这种方式,在原文中不够直观  
> 不过这次教学必须使用`tab`不然无法为你显示` ``` `

    ```
        这里就是代码区块了
        它可以有多行
    ```

*下面我们演示代码区块*

```     
    这里就是代码区块了
    它可以有多行
```
    
> 注意, 处于这个区块的代码,  Markdown 语法不会被转换

### 一整块代码(带有颜色)

在上面代码块第一个三点的指定渲染格式即可. ` ```C# `

    ```C#
    public void Main()
    {
        Log("本文来自 ML-Blog");
    }
    ```
*转化之后就带有C#标准的颜色了*
```C#
public void Main()
{
    Log("本文来自 ML-Blog");
}
```

> GitHub支持的渲染格式请前往[语言YAML文件][GitHub_linguist_languages.yml]

## 分隔线

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：

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

## 链接

### 超链接

```
[GitHub Docs 写作文档][github.docs.writing]  <!-- //[描述][定义的链接] -->
[github.docs.writing]:https://docs.github.com/cn/github/writing-on-github  "hexo.io 官网" <!-- //[定义的链接][实际网址] -->
```

*转化后如下*

[GitHub Docs 写作文档][github.docs.writing]
<!-- [github.docs.writing]:https://docs.github.com/cn/github/writing-on-github  "开始在 GitHub 上编写"  //这个地址已经在最下面定义了,此处就不重复定义了 -->

>此时鼠标移动带蓝色标签上, 可以看到描述内容

可以看到,使用一个链接需要定义,但是定义可以在使用之后,所以一般将定义放置与文章末尾,方便统一管理.  
而且`github.docs.writing`是我定义的别名,这个别名是可以随意修改的,多了一层更方便管理
这样`github.docs.writing`这个别名还可与多处使用,推荐这种定义方式.

> 还可与使用相对链接来跳转到本地文件

```
[hello](hello-world.md) <!-- //[描述][定义的链接] -->
[hello-world.md]:hello-world.md "hello-world.md" <!-- //[定义的链接][实际网址] -->
```

*实际效果如下*

[hello-world](hello-world.md)

### 快速链接

还有一种方式可以省略定义步骤.  
使用`[]`包裹要显示的内容,跟上`()`包裹链接地址和描述内容,  
描述地址和链接地址需要一个空格隔开, 描述地址需要使用`""`括住.

`[通往GitHub](https://www.github.com "github链接") `

*如下*

[通往GitHub](https://www.github.com "github链接")

> 这样就是可以直接在正文中看到链接,但是管理不是很方便,后面要修改就要全文找.

### 图片链接

事实上, 图片和链接是一回事, 你只需要在链接的语法最前面加上`!`

> 下面使用快速链接来演示,实际也可以使用定义的方式.各位可以自行尝试

```
![UserMingHaoLi GitHub stats](https://github-readme-stats.vercel.app/api?username=UserMingHaoLi)
```

*可以看到图片显示出来了*

![UserMingHaoLi GitHub stats](https://github-readme-stats.vercel.app/api?username=UserMingHaoLi)

> 此图片来自[github-readme-stats][github-readme-stats]

> 图片也可以使用相对链接来显示本地图片

```
![UserMingHaoLi GitHub stats](模板/UML类图模板.png)
```

*就像这样*

![UserMingHaoLi GitHub stats](模板/UML类图模板.png)

## 复选框

也叫代办项,或者任务列表 在任何列表的基础上使用 空格`[空格]`,这里使用`-`空格`[空格]`来表示一个未办项目, 替换`[x]`则为已办项目  
支持`tab`缩进多级代办

```
1. [ ] 未办理项目
    * [ ] 未办理项目二级
- [x] 已办理项目
    + [x] 已办理项目二级
```

*实际上由于不是用一种列表,下面的已办和未办不是同一个代办列表, 所以最好是使用同样的列表符号*

1. [ ] 未办理项目
    * [ ] 未办理项目二级
- [x] 已办理项目
    + [x] 已办理项目二级

如果任务列表项说明以括号开头，则需要使用 `\` 进行规避：

```
- [ ] \(Optional) 打开后续议题
```

- [ ] \(Optional) 打开后续议题

## html代码

在Markdown中书写`HTML`代码

```
<html>
<!--在这里插入内容-->
<p>nihao</p>
</html>
```

*如下,具体内容可自行尝试*

<html>
<!--在这里插入内容-->
<p>nihao</p>
</html>

## 表格

制表需要使用大量的`|` 用于分隔每项 
第一行是表头, 会加粗, 
第二行是对其方式, 使用`---`表示居中,`-`最少三个  
将最左边或者最右边的`-`替换为`:`表示居于其中一边  
最后自行往下写就可以了, 注意使用`|`分隔

```
| header 1       |        header 2 |
| :------------- | --------------: |
| row 1 col 1232 | row 1 col 22323 |
| row 2 col 1    |     row 2 col 2 |
```

*如果在文中想使用`|`则需要配合`\`. 变为`\|`*

| header 1       |        header 2 |
| :------------- | --------------: |
| row 1 col 1232 | row 1 col 22323 |
| row 2 col 1    |     row 2 col 2 |

在`Markdown All in One`插件的帮助下,你可以使用`Alt` + `Shift` + `F` 来让表格变得容易阅读,具体可以参阅[文档][markdown-all-in-one_Link]

## 注释

```
<!--注释-->
```

*注释当然看不到啦~~~*

<!--注释-->

# 其他非GitHub支持的MarkDown内容

> 详细可看我的另一篇文章 [Markdown进阶][Markdown进阶]

# 拼写提示

1. `Markdown`的M大写但d没有大写,表示这是一整个单词,而不是`Mark`和`Down`
2. `VSCode`的VSC为大写,其中`VS`表示`visualstudio`的缩写,`C`表示`Code`这整个单词的开头
3. `GitHub`的G和H是大写的,表示分为`Git`和`Hub`两词

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]


<!-- 图片 -->


<!-- 链接 -->

<!-- VSCode篇 -->
[VSCode_Link]:https://code.visualstudio.com/ "VSCode 官网下载"
[vscode-language-pack-zh-hans_Link]:https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans "VSCode 中文语言包 插件 官网"
[markdown-all-in-one_Link]:https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one "VSCode Markdown All in One 插件 官网"
[vscode-paste-image_Link]:https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image "VSCode Paste Image 插件 官网"

<!-- GitHub For Markdown篇 -->
[markdown.cn]:http://www.markdown.cn "markdown.cn 官网"
[github.docs.writing]:https://docs.github.com/cn/github/writing-on-github  "开始在 GitHub 上编写"
[gfm.example]:https://github.github.com/gfm "gfm规范演示"
[GitHub_linguist_languages.yml]:https://github.com/github/linguist/blob/master/lib/linguist/languages.yml "GitHub支持的语言渲染格式列表"
[hello-world.md]:hello-world.md "hello-world.md"
[github-readme-stats]:https://github.com/anuraghazra/github-readme-stats "github-readme-stats"
[Markdown进阶]:Markdown进阶.md "Markdown进阶-来自ML"
<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
