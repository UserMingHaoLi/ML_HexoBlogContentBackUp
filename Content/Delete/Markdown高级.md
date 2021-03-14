---
title: Markdown高级
date: 2019-06-05 22:18:00
updated: 2020-03-27 21:31:00
categories:
	- Markdown
tags: 
	- Markdown
---

[toc]

# Markdown高级

每次移动一篇文章, 我就自己阅读一遍, 也是希望能加深记忆, 为我自己好吧.

> 内容来自 <http://www.markdown.cn> 是Markdown原生支持的最基础

<!--more-->

## 斜体

使用一组`*`将想要显示的内容括起来

`*内容*`

*内容*

## 链接

### 快速链接

使用`[]`包裹要显示的内容,跟上`()`包裹链接地址和描述内容,  
描述地址和链接地址需要一个空格隔开, 描述地址需要使用`""`括住.

`[通往百度](https://www.baidu.com "百度链接") `

[通往百度](https://www.baidu.com "百度链接") 

>此时鼠标移动带蓝色标签上, 可以看到描述内容

### 常规链接

通常, 我们并不会立即的跟上链接, 而只是做一个标记, 在统一的地方跟上链接.  
首先你使用一个`[]`包裹链接显示的内容, ~~之后跟上一个空格,~~  
再加上`[]`包裹的标签名.  
之后你可以随时在任何地方再次声明标签的链接.  
你需要先使用`[]`包裹标签, 跟上一个`:`, 接上空格, 之后是链接地址,  
再跟上空格之后`""`包裹标签描述

```
[通往百度][link1]

[link1]: https://www.baidu.com "baidu"
```

[通往百度][link1]

[link1]: https://www.baidu.com "baidu"

>注意, 你只需要在需要的地方做第一个声明, 第二个声明可以在任何地方

## 代码

### 小段代码

你可以是用` `` `来包裹代码区块


<html>
`print("内容");`
</html>

`print("内容");`

### 大段代码

使用` ``` `来开启和结束

    ```
    class cname{
        public void Main()
        {
            print("内容"); 
            if(true)
            {
                .......
            }  
        }
    }
    ```

```java
class cname{
    public void Main()
    {
        print("内容"); 
        if(true)
        {
            .......
        }  
    }
}
```

## 图片

事实上, 图片和链接是一回事, 你只需要在链接的语法最前面加上`!`

`![显示百度](https://www.baidu.com/img/bd_logo1.png "百度logo")`

![显示百度](https://www.baidu.com/img/bd_logo1.png "百度logo")

```
![显示百度] [image1]

[image1]: https://www.baidu.com/img/bd_logo1.png "百度logo"
```

![显示百度] [image1]

[image1]: https://www.baidu.com/img/bd_logo1.png "百度logo"

> 那么基础部分完毕, 欲知后事如何,且看进阶篇.