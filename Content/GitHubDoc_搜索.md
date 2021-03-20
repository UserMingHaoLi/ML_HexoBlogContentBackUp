

---
title: hexo模板
date: 2021-03-12 22:52:00
updated: 2021-03-12 22:52:00
categories:
	- hexo
tags: 
	- hexo
---

# 在 GitHub 上搜索

可以前往 [GitHub高级搜索](https://github.com/search/advanced)

## 搜索语法

你可以使用`>`,`>=`,`<`,`<=`这些来进行过滤

```c
cats stars:>=1000
cats topics:<=5
```

或是简写方式,他们呢是等同的
```c
cats stars:1000..* 
cats topics:*..5
```

稍微扩展一下,还可与指定范围
```c
cats stars:10..50
```

可以使用`YYYY-MM-DD`格式查询日期

```c
cats created:>=2017-04-01
```
>这同样适用于上述简写和范围方式

时间也可以指定小时和区间
```c
cats created:2016-03-21T14:11:00Z..2016-04-07T20:45:00Z 
//匹配在 2016 年 3 月 21 日下午 2:11 与 2016 年 4 月 7 日晚上 8:45 之间创建的议题。
```

如果想排除某些结果,可以使用`NOT` 关键字
```c
hello NOT world 
//匹配含有 "hello" 字样但不含有 "world" 字样的仓库。
```

还可以通过`-`前缀来排除搜索结果的子集
```c
cats stars:>10 -language:javascript 
//匹配含有 "cats" 字样、有超过 10 个星号但并非以 JavaScript 编写的仓库。
```

还可以搜索参与者

```c
mentions:defunkt -org:github 
//匹配提及 @defunkt 且不在 GitHub 组织仓库中的议题
```

如果搜索含有空格的查询，您需要用引号将其括起来。 例如

* `cats NOT "hello world"` 匹配含有 "cats" 字样但不含有 "hello world" 字样的仓库。
* `build label:"bug fix"` 匹配具有标签 "bug fix"、含有 "build" 字样的议题。

---

如果搜索查询包含需要用户名的限定符，例如 `user`、`actor` 或 `assignee`，您可以使用任何 GitHub 用户名指定特定人员，或使用 @me 指定当前用户。

```c
author:nat //匹配 @nat 创作的提交。
is:issue assignee:@me //匹配已分配给结果查看者的议题
```

---

在 GitHub 上搜索时，查询的长度有一些限制：

* 不支持长度超过 256 个字符的查询
* 您无法使用超过五个 AND、OR 或 NOT 运算符构造查询

## 搜索排序

你可以使用`sort`关键字来排序

所有排序都可以以`-desc`或`-asc`后缀来安排顺序

`sort:interactions` 限定符按最高反应和评论总数排序。

```c
org:github sort:interactions 
//匹配 GitHub 所拥有仓库中的议题，按最高反应和评论总数排序。
```

`sort:reactions` 限定符按反应数量或类型排序。
>注意,支持表情
```c
org:github sort:reactions 
//匹配 GitHub 所拥有仓库中的议题，按最高反应数量排序。
org:github sort:reactions-heart 
//匹配 GitHub 所拥有仓库中的议题，按最多红心 (❤️) 反应数排序。
```

`sort:author-date` 限定符按作者日期降序或升序排序。

```c
feature org:github sort:author-date 
//匹配 GitHub 所拥有仓库中含有 "feature" 字样的提交，按作者日期降序排序。
```

`sort:committer-date` 限定符按提交者日期降序或升序排序

```c
feature org:github sort:committer-date 
//匹配 GitHub 所拥有仓库中含有 "feature" 字样的提交，按提交者日期降序排序。
```

`sort:updated` 限定符按项目最近更新日期排序。

```c
feature sort:updated 
//匹配含有 "feature" 字样的仓库，按最近更新日期排序。
```

## 在 GitHub 上查找文件


文件查找器的结果不包括某些目录，例如 `build`、`log`、`tmp` 和 `vendor`。

导航到仓库的主页面,单击 **Go to file（转到文件）**

## 搜索仓库

要在搜索结果中包括复刻，您需要将 `fork:true` 或 `fork:only` 添加到查询

通过 `in` 限定符，您可以将搜索限制为仓库名称、仓库说明、自述文件内容或这些的任意组合。 如果省略此限定符，则只搜索仓库名称和说明。

```c
jquery in:name 
//匹配仓库名称中含有 "jquery" 的仓库。
jquery in:name,description 
//匹配仓库名称或说明中含有 "jquery" 的仓库。
jquery in:readme 
//匹配仓库自述文件中提及 "jquery" 的仓库。
repo:octocat/hello-world 
//匹配特定仓库名称。
```

要在特定用户或组织拥有的所有仓库中搜索，您可以使用 `user` 或 `org` 限定符


```c
user:defunkt forks:>100 
//匹配来自 @defunkt、拥有超过 100 复刻的仓库。
org:github 
//匹配来自 GitHub 的仓库。
```

`size` 限定符使用大于、小于和范围限定符查找匹配特定大小（以千字节为单位）的仓库。 

```c
size:1000 //匹配恰好为 1 MB 的仓库。
size:>=30000 //匹配至少为 30 MB 的仓库。
size:<50 //匹配小于 50 KB 的仓库。
size:50..120 //匹配介于 50 KB 与 120 KB 之间的仓库。
```

按关注者数量搜索可以使用`followers`

```c
node followers:>=10000 
//匹配有 10,000 或更多关注者提及文字 "node" 的仓库。
styleguide linter followers:1..10 
//匹配拥有 1 到 10 个关注者并且提及 "styleguide linter" 一词的的仓库。
```

按复刻数量搜索使用`forks`

```c
forks:5 //匹配只有 5 个复刻的仓库。
forks:>=205 //匹配具有至少 205 个复刻的仓库。
forks:<90 //匹配具有少于 90 个复刻的仓库。
forks:10..20 //匹配具有 10 到 20 个复刻的仓库。
```

按星星搜索使用`stars`

```c
stars:500 
//匹配恰好具有 500 个星号的仓库。
stars:10..20 
//匹配具有 10 到 20 个星号、小于 1000 KB 的仓库。
stars:>=500 fork:true language:php 
//匹配具有至少 500 个星号，包括复刻的星号（以 PHP 编写）的仓库。
```

按仓库创建或上次更新时间搜索
* `created`表示创建
* `pushed`表示推送
>这些时间任然可以精确到秒并且支持区间形式
```c
webos created:<2011-01-01 
//匹配具有 "webos" 字样、在 2011 年之前创建的仓库。
css pushed:>2013-02-01 
//匹配具有 "css" 字样、在 2013 年 1 月之后收到推送的仓库。
```

可以根据仓库中代码的语言搜索仓库,使用`language`

```c
rails language:javascript 
//匹配具有 "rails" 字样、以 JavaScript 编写的仓库。
```

可以找到按特定主题分类的所有仓库,`topic`
>GitHub有topic页面,如: <https://github.com/topics/unity>

```c
topic:unity
//匹配已归类为 "unity" 主题的仓库
```

如果想搜索的项目横跨多个主题,使用`topics`

```c
topics:5 
//匹配具有五个主题的仓库。
topics:>3 
//匹配超过三个主题的仓库。
```

按许可搜索`license`

```c
license:apache-2.0 
//匹配根据 Apache License 2.0 授权的仓库。
```

据仓库的可见性过滤搜索
* is:public
* is:internal
* is:private
>普通人不太用得上.

基于仓库是否为镜像搜索
* mirror:true
* mirror:false

基于仓库是否已存档搜索
* archived:true
* archived:false

---
还有个有趣的.  
基于标签搜索,现在好像就支持两种标签

```c
good-first-issues:>2 javascript 
//匹配具有超过两个标签为 good-first-issue 的议题且包含 "javascript" 字样的仓库。

help-wanted-issues:>4 react 
//匹配具有超过四个标签为 help-wanted 的议题且包含 "React" 字样的仓库。
```

## 搜索主题

```c
is:curated javascript 
//匹配精心策划且含有 "javascript" 字样的主题。
is:featured javascript 
//匹配 https://github.com/topics/ 上提供且含有 "javascript" 字样的主题。
is:not-curated javascript 
//匹配没有额外信息（例如说明或徽标）且含有 "javascript" 字样的主题。
is:not-featured javascript 
//匹配 https://github.com/topics/ 上未提供且含有 "javascript" 字样的主题。
repositories:>5000 匹配超过 5000 个仓库的主题。
Serverless created:>2019-01-01 
//匹配含有 "serverless" 字样、在 2018 年之后创建的主题。
```

## 搜索代码

由于搜索代码的复杂性，执行搜索的方式有一些限制：

* 您必须登录到 GitHub 上的用户帐户才能在所有公共仓库中搜索代码。
* 复刻中的代码仅当复刻的星号超过父级仓库时可搜索。 星号少于父仓库的复刻不为代码搜索编索引。 要在搜索结果中包括星号比其父项多的复刻，您需要将 fork:true 或 fork:only 添加到查询。 更多信息请参阅“在复刻中搜索”。
* 只有默认分支被索引用于代码搜索。
* 只有小于 384 KB 的文件可搜索。
* 只能搜索少于 500,000 个文件的仓库。
* 只能搜索去年有活动或已在搜索结果中返回的仓库。
* 除了 filename 搜索以外，搜索源代码时必须始终包括至少一个搜索词。 例如，搜索 language:javascript 无效，而搜索 amazing language:javascript 有效。
* 搜索结果最多可显示同一文件的两个分段，但文件内可能有更多结果。
* 您无法使用以下通配符作为搜索查询的一部分：. , : ; / \ ` ' " = * ! ? # $ & + ^ | ~ < > ( ) { } [ ]. 搜索只会忽略这些符号。

先来个小菜
>这里有一个技巧,如果你连续使用同个类型匹配,可以省略前缀,如`in:file,path`
```c
octocat in:file 
//匹配文件内容中出现 "octocat" 的代码。
octocat in:path 
//匹配文件路径中出现 "octocat" 的代码。
octocat in:file,path 
//匹配文件内容或文件路径中出现 "octocat" 的代码。
```

然后是基于用户的搜索

```c
extension:rb 
//匹配来自 @defunkt、以 .rb 结尾的代码。
org:github extension:js 
//匹配来自 GitHub、以 .js 结尾的代码。
repo:mozilla/shumway extension:as 
//匹配来自 @mozilla 的 shumway 项目、以 .as 结尾的代码。
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->



<!-- 链接 -->

[GitHubDeskTop]:https://docs.github.com/cn/desktop "GitHubDeskTop官方文档"
[GitHubDeskTopDownLoad]:https://desktop.github.com/ "GitHubDeskTop下载"

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"




