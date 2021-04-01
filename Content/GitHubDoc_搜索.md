---
title: GitHubDoc_搜索
date: 2021-03-20 22:52:00
updated: 2021-03-21 11:53:00
id: ml-20210320-225200-g8
categories:
	- Docs
tags: 
	- GitHub
---

# 在 GitHub 上搜索

可以前往 [GitHub高级搜索](https://github.com/search/advanced)

<!--more-->

## 搜索语法

你可以使用`>`,`>=`,`<`,`<=`这些来进行过滤

```c
cats stars:>=1000
cats topics:<=5
```

或是简写方式,他们是等同的
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

> GitHub只为Start数量大于源仓库的复刻添加索引.

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
>这些时间仍然可以精确到秒并且支持区间形式
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
> 这个也用的很多,下面不再重复
* is:public
* is:internal
* is:private

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
repositories:>5000 
//匹配超过 5000 个仓库的主题。
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
> 后续会经常用到.不再重复
```c
user:defunkt extension:rb 
//匹配来自 @defunkt、以 .rb 结尾的代码。
org:github extension:js 
//匹配来自 GitHub、以 .js 结尾的代码。
repo:mozilla/shumway extension:as 
//匹配来自 @mozilla 的 shumway 项目、以 .as 结尾的代码。
```

按文件位置搜索

```c
octocat filename:readme path:/ 
//匹配位于仓库根目录级别且含有 "octocat" 字样的 readme 文件。
form path:cgi-bin language:perl 
//匹配位于 cgi-bin 目录或其任何子目录中且含有 "form" 字样的 Perl 文件。
console path:app/public language:javascript 
//匹配 app/public 目录或其任何子目录（即使其位于 app/public/js/form-validators 中）中且含有 "console" 字样的 JavaScript 文件。
```

按语言搜索,我们经常用的`language:LANGUAGE`

```c
element language:xml size:100 
//匹配标记为 XML 且恰好有 100 个字节的并含有 "element" 字样的代码。
display language:scss 
//匹配标记为 SCSS 且含有 "display" 字样的代码。
org:mozilla language:markdown 
//匹配标记为 Markdown 且来自所有 @mozilla 仓库的代码。
```

按文件大小搜索,之前就介绍的`size:n`

```c
function size:>10000 language:python 
//匹配含有 "function" 字样、以 Python 编写、位于大于 10 KB 的文件中的代码。
```

按文件名搜索`filename:FILENAME`

```c
filename:linguist 
//匹配名为 "linguist" 的文件。
filename:.vimrc commands 
//匹配含有 "commands" 字样的 .vimrc 文件。
filename:test_helper path:test language:ruby 
//匹配 test 目录内名为 test_helper 的 Ruby 文件。
```

按文件扩展名搜索`extension:EXTENSION`

```c
form path:cgi-bin extension:pm 
//匹配含有 "form" 字样、位于 cgi-bin 下且具有 .pm 文件扩展名的代码。
icon size:>200000 extension:css 
//匹配大于 200 KB、以 .css 结尾且含有 "icon" 字样的文件。
```

## 搜索提交

> 当您搜索提交时，仅搜索仓库的默认分支

使用 `author` 或 `committer` 限定符按特定用户查找提交

```c
author:defunkt 
//匹配 @defunkt 创作的提交。
committer:defunkt 
//匹配 @defunkt 提交的提交。
```

*或者也可使用搜索形式*

```c
author-name:wanstrath 
//匹配作者姓名中包含 "wanstrath" 的提交。
committer-name:wanstrath 
//匹配提交者姓名中包含 "wanstrath" 的提交。
```

*还有Email形式*

```c
author-email:chris@github.com 
//匹配 chris@github.com 创作的提交。
committer-email:chris@github.com 
//匹配 chris@github.com 提交的提交。
```

按创作或提交日期搜索
>还记得时间用法吗?
```c
author-date:<2016-01-01 
//匹配 2016-01-01 之前创作的提交。
committer-date:>2016-01-01 
//匹配 2016-01-01 之后的提交。
```

merge 限定符过滤合并提交
* merge:true
* merge:false

hash 限定符匹配具有指定 SHA-1 哈希的提交

```c
hash:124a9a0ee1d8f1e15e833aff432fbb3b02632105 
//略
```

parent 限定符匹配其父项具有指定 SHA-1 哈希的提交

```c
parent:124a9a0ee1d8f1e15e833aff432fbb3b02632105 
//略
```

tree 限定符匹配具有指定 SHA-1 git 树哈希的提交

```c
tree:99ca967 
//匹配引用树哈希 99ca967 的提交
```

在用户或组织的仓库内搜索,`user`,`org`,`repo`  
**老三样 略**

按仓库可见性过滤
**老三样 略**

## 搜索议题或拉取请求

默认会全部搜索,使用`type`,`is`来筛选种类

```c
cat type:pr 
//匹配含有 "cat" 字样的拉取请求。
github commenter:defunkt type:issue 
//匹配含有 "github" 字样且由 @defunkt 评论的议题。
event is:pr 
//匹配含有 "event" 字样的拉取请求。
is:issue label:bug is:closed 
//匹配具有标签 "bug" 的已关闭议题。
```

通过 `in` 限定符，您可以将搜索限制为标题、正文、评论或这些的任意组合。 如果省略此限定符，则标题、正文和评论全部搜索。

```c
warning in:title 
//匹配其标题中含有 "warning" 的议题。
error in:title,body 
//匹配其标题或正文中含有 "error" 的议题。
shipit in:comments 
//匹配其评论中提及 "shipit" 的议题。
```

`user` 或 `org` 搜索指定用户和仓库

**老三样 略**

按开放或关闭状态搜索,`state`,`is`

```c
libraries state:open mentions:vmg 
//匹配提及 @vmg 且含有 "libraries" 字样的开放议题。
design state:closed in:body 
//匹配正文中含有 "design" 字样的已关闭议题。
performance is:open is:issue 
//匹配含有 "performance" 字样的开放议题。
android is:closed 
//匹配含有 "android" 字样的已关闭议题和拉取请求。
```

按仓库可见性过滤  
**老三样,上面讲过了**

`author` 限定符

**也讲过了**

按受理人搜索`assignee`

```c
assignee:vmg repo:libgit2/libgit2 匹配分配给 @vmg 的 libgit2 项目 libgit2 中的议题和拉取请求。
```

按提及搜索`mentions`

```c
resque mentions:defunkt 
//匹配含有 "resque" 字样、提及 @defunkt 的议题。
```

按团队提及搜索`team`

```c
team:jekyll/owners 
//匹配提及 @jekyll/owners 团队的议题。
team:myorg/ops is:open is:pr 
//匹配提及 @myorg/ops 团队的打开拉取请求。
```

按评论者搜索 `commenter `

```c
github commenter:defunkt org:github 
//匹配位于 GitHub 拥有的仓库中、含有 "github" 字样且由 @defunkt 评论的议题。
```

使用 `involves` 限定符查找以某种方式涉及特定用户的议题

```c
involves:defunkt involves:jlord 
//匹配涉及 @defunkt 或 @jlord 的议题。
NOT bootstrap in:body involves:mdo 
//匹配涉及 @mdo 且正文中未包含 "bootstrap" 字样的议题。
```

搜索链接的议题和拉取请求

```c
repo:desktop/desktop is:open linked:pr 
//匹配 desktop/desktop 仓库中通过关闭引用链接到拉取请求的开放议题。
repo:desktop/desktop is:closed linked:issue 
//匹配 desktop/desktop 仓库中链接到拉取请求可能已关闭的议题的已关闭拉取请求。
repo:desktop/desktop is:open -linked:pr 
//匹配 desktop/desktop 仓库中未通过关闭引用链接到拉取请求的开放议题。
repo:desktop/desktop is:open -linked:issue 
//匹配 desktop/desktop 仓库中未链接至拉取请求可能关闭的议题的开放拉取请
```

按标签搜索`label`,之前一直用的

```c
label:"help wanted" language:ruby 
//匹配标签为 "help wanted"、位于 Ruby 仓库中的议题。
broken in:body -label:bug label:priority 
//匹配正文中含有 "broken" 字样、没有 "bug" 标签但有 "priority" 标签的议题。
label:bug label:resolved 
//匹配含有 "bug" 和 "resolved" 标签的议题。
```

按里程碑搜索`milestone`

```c
milestone:"overhaul" 
//匹配位于名为 "overhaul" 的里程碑中的议题。
milestone:"bug fix" 
//匹配位于名为 "bug fix" 的里程碑中的议题。
```

按项目板搜索`project `

```c
project:github/57 
//匹配 GitHub 拥有的、与组织项目板 57 关联的议题。
project:github/linguist/1 
//匹配与 @github 的 linguist 仓库中的项目板 1 关联的议题。
```

按提交状态搜索`status`

```c
language:go status:pending 
//匹配在状态为待定的 Go 仓库中打开的拉取请求。
is:open status:success finally in:body 
//匹配正文中含有 "finally" 字样、具有成功状态的打开拉取请求。
created:2015-05-01..2015-05-30 status:failure 
//匹配在 2015 年 5 月打开、具有失败状态的拉取请求。
```

按提交 SHA 搜索
```c
e1109ab 
//匹配具有开头为 e1109ab 的提交 SHA 的拉取请求。
0eff326d6213c is:merged 
//匹配具有开头为 0eff326d6213c 的提交 SHA 的合并拉取请求
```
> 这里可能是掉了一个 `SHA:` 头部

按分支名称搜索

```c
head:change is:closed is:unmerged 
//匹配从名称以 "change" 字样开头的已关闭分支打开的拉取请求。
base:gh-pages 
//匹配合并到 gh-pages 分支中的拉取请求。
```

按语言搜索`language:LANGUAGE`

按评论数量搜索`comments:n`

按交互数量搜索`interactions:n`

按反应数量搜索`reactions:n`

//搜索草稿拉取请求 **略**

基于拉取请求的审查状态（无、必需、批准或请求更改）

```c
type:pr review:none 
//匹配尚未审查的拉取请求。
type:pr review:required 
//匹配需要审查然后才能合并的拉取请求。
type:pr review:approved 
//匹配审查者已批准的拉取请求。
type:pr review:changes_requested 
//匹配审查者已请求更改的拉取请求。
type:pr reviewed-by:gjtorikian 
//匹配特定人员审查的拉取请求。
type:pr review-requested:benbalter 
//匹配特定人员申请审查的拉取请求。 申请的审查者在其审查拉取请求后不再在搜索结果中列出。 如果申请的人员在申请审查的团队中，则该团队的审查请求也将在搜索结果中显示。
type:pr team-review-requested:atom/design 
//匹配已审查团队 atom/design 请求的拉取请求。 申请的审查者在其审查拉取请求后不再在搜索结果中列出。
```

基于创建时间或上次更新时间过滤议题
* `created:YYYY-MM-DD`
* `updated:YYYY-MM-DD`
*还可以按关闭时间和合并时间*
* `merged:YYYY-MM-DD`
* `closed:YYYY-MM-DD`

使用 is 限定符基于拉取请求已合并还是未合并进行过滤
* is:merged
* is:unmerged

基于仓库是否已存档搜索
* archived:true	
* archived:false

基于对话是否已锁定搜索
* is:locked
* is:unlocked

按缺少的元数据搜索 `no`

```c
priority no:label 
//匹配没有任何标签且含有 "priority" 字样的议题和拉取请求。
sprint no:milestone type:issue 
//匹配未与含有 "sprint" 字样的里程碑关联的议题。
important no:assignee language:java type:issue 
//匹配未与受理人关联、含有 "important" 字样且位于 Java 仓库中的议题。
build no:project 
//匹配未与项目板关联、含有 "build" 字样的议题。
```

## 搜索讨论

按标题、正文或评论搜索
* in:title
* in:body
* in:comments

在用户或组织的仓库内搜索

**老三样 `user`,`org`,`repo`**

按仓库可见性过滤

**老三样 略**

按作者搜索`author:USERNAME`

按评论者搜索`commenter:USERNAME`

按涉及讨论的用户搜索`involves:USERNAME`

按评论数量搜索`comments:n`

按交互数量搜索`interactions:n`

按反应数量搜索`reactions:n`

按讨论创建或上次更新时间搜索
* created:YYYY-MM-DD
* updated:YYYY-MM-DD

## 搜索用户

仅搜索用户或组织
**老三样 略**

使用 user 或 org 限定符根据个人用户或组织帐户的名称过滤搜索  
通过 in 限定符，您可以将搜索限制为用户名 (login)、全名、公共电子邮件或这些的任意组合

```c
user:octocat 
//匹配用户名为 "octocat" 的用户。
org:electron type:users 
//匹配 Electron 组织的帐户名。
kenya in:login 
//匹配其用户名中含 "kenya" 字样的用户。
bolton in:name 
//匹配其真实姓名含有 "bolton" 字样的用户。
fullname:nat friedman 
//匹配全名为 "Nat Friedman" 的用户。 注：此搜索限定符区分空格。
data in:email 
//匹配其电子邮件中含有 "data" 字样的用户。
```

按用户拥有的仓库数量搜索`repos:n`

按位置搜索`location:LOCATION`

按仓库语言搜索`language:LANGUAGE`

按用户帐户创建时间搜索`created:YYYY-MM-DD`

按关注者数量搜索`followers:n`

## 搜索包

搜索用户或组织的包
* user:USERNAME
* org:ORGNAME

按包可见性过滤
* is:public
* is:private

## 搜索 wiki

在用户或组织的仓库内搜索

**老三样 `user`,`org`,`repo`**

在 wiki 页面标题或正文文本中搜索
* in:title
* in:body

按上次更新日期搜索`updated:YYYY-MM-DD`

## 在复刻中搜索

默认情况下，forks 不会在搜索结果中显示。 如果复刻满足特定条件，您可以选择在仓库搜索以及在代码搜索中包括它们。

仅当复刻具有比父仓库更多的星号时，才会为代码搜索编索引。 您无法在比父项具有更少星号的复刻中搜索代码。 

`fork:true` 限定符查找匹配搜索查询的所有结果，包括复刻。 `fork:only` 限定符仅查找匹配搜索查询的复刻。


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->



<!-- 链接 -->



<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"




