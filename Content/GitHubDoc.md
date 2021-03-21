---
title: GitHubDoc
date: 2021-03-17 22:52:00
updated: 2021-03-17 22:52:00
categories:
	- Docs
tags: 
	- GitHub
---

# 下载GitHubDeskTop

内容来自 [GitHubDeskTop官方文档][GitHubDeskTop]

下载请前往[desktop.github.com][GitHubDeskTopDownLoad]

<!--more-->

# 参与项目

如果你已经知道要参与哪些项目，可通过访问 `github.com/<owner>/<repository>/contribute` 查找该仓库中便于初学者参与的议题。 例如，您可以在 <https://github.com/electron/electron/contribute> 上找到第一次参与 `electron/electron` 的方法。

# 编写

## 提及人员和团队

使用`@`加上其用户名或团队名称.这将通知目标你提及了他.
```
@github/support
```
## 引用议题和拉取请求

使用`#`你可以引用指定的议题和拉取请求
```
#26
```

## 使用表情符号

通过键入 `:EmojiCode:` 可在您的写作中添加表情符号。
```
:+1:
:shipit:
```

# 使用已保存回复

允许你创建一批`快捷用语`帮助你进行回复  
如果您经常反复添加相同的评论，您可以创建已保存回复

1. 在任何页面的右上角，单击您的个人资料照片，然后单击 **Settings（设置）**
2. 在左侧边栏中，单击 **Saved replies（已保存回复）**
3. 在**Add a saved reply（添加已保存回复**下，添加已保存回复的标题
4. 在**Write（撰写 ）**字段中，添加要用于已保存回复的内容。 有关在 GitHub 上撰写的更多信息，请参阅**基本撰写和格式语法**
5. 要查看回复，请单击 **Preview（预览）**
6. 单击 **Add saved reply（添加已保存回复）**


# 存档仓库

如若你不再维护某个仓库, 可以存档它.  
但记得更改自述文件,关闭讨论和提交.

之后使用仓库设置中的 **Archive this repository（存档此仓库）**

返回时也可以使用 **Unarchive this repository(取消存档此仓库)**

# 使用Git

## 在Git中设置用户名

*名称仅影响未来的提交，不会更改用于过去提交的名称*  

```
$ git config --global user.name "Mona Lisa"
```

上述命令添加了`global`标志, 表示他是一个全局命令, 对所有仓库生效.

> 如果想在Git中缓存GitHub凭证,请使用SSH方式.

## 设置Git编辑器

*这里设置VSCode作为编辑器*

```
$ git config --global core.editor "code --wait"
```

*也可以使用Notepad++*

```
$ git config --global core.editor "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

## 配置 Git 处理行结束符

不同的操作系统有不同的*结束符*,或者对同一个结束符有不同的处理方式.  

在使用 Git 和 GitHub 协作处理项目时，Git 可能产生意外结果，例如，您在 Windows 计算机上操作，而您的协作者是在 OS X 中做的更改。

所以我们需要标准化结束符

您可以将 Git 配置为自动处理行结束符，以便与使用不同操作系统的人员有效地协作。

```
$ git config --global core.autocrlf true
```

为确保仓库中的所有行结束符与新配置匹配，请使用 Git 备份文件，删除仓库中的所有文件（.git 目录除外），然后一次性恢复所有文件。

## 忽略文件

`.gitignore`指示 Git 在您进行提交时要忽略哪些文件和目录  

GitHub的`github/gitignore`库中有一些常见规则可以参考

## 推送提交到远程仓库

`git push` 命令使用两个参数： 
* 远程命令，如 origin
* 分支名称，如 main

例如 运行 `git push origin main` 来推送本地更改到在线仓库

## git clone

运行 git clone 时，将发生以下操作：

* 创建名为 repo 的文件夹
* 将它初始化为 Git 仓库
* 创建名为 origin 的远程仓库，指向用于克隆的 URL
* 将所有的仓库文件和提交下载到那里
* 默认分支已检出

## 从远程仓库获取更改

```
$ git fetch remotename
# 获取远程仓库的更新
```

## 合并更改到本地分支

```
$ git merge remotename/branchname
# 将在线更新与您的本地工作进行合并
```

> `git pull` 是在同一个命令中完成 `git fetch` 和 `git merge` 的便捷方式。

## 添加远程仓库到本地

git remote add 命令使用两个参数：

* 远程命令，如 origin
* 远程 URL，如 https://github.com/user/repo.git

```
$ git remote add origin https://github.com/user/repo.git
```

> `git remote set-url` 命令可更改现有远程仓库的 URL  
> `git remote -v` 罗列当前远程仓库

## 重命名远程

git remote rename 命令使用两个参数：

* 现有的远程名称，例如 origin
* 远程的新名称，例如 destination

```
$ git remote rename origin destination
# 将远程名称从 'origin' 更改为 'destination'
```

## 删除远程

```
$ git remote rm destination
# 删除远程
```

> 它只是从本地仓库中删除远程及其引用,不会删除服务器上的仓库.

# 使用 GitHub Desktop 创建合作提交

在提交栏的左下角有一个带+号的小人,点一下挨个@即可

# 在 GitHub 上搜索

请前往 [在 GitHub 上搜索][GitHub_Search_localhost]

# 在 GitHub 上使用 Subversion

详细请自行查阅文档,感觉没什么必要..

# 在 GitHub 上发布项目

就是经常下载的 `Releases` 部分

发行版中包含的每个文件都必须在 2 GB 下。 发行版的总大小和带宽使用没有限制。

1. 在 GitHub 上，导航到仓库的主页面。
2. 对于文件列表的右侧，单击 **Releases（发行版）**或 **Latest release（最新发行版）**
3. 单击 **Draft a new release（草拟新发行版）**
4. 键入发行版的版本号
5. 使用下拉菜单，选择包含要发布的项目的分支
6. 键入发行版的标题和说明
7. 要在发行版中包含二进制文件（例如已编译的程序），请在二进制文件框中拖放或手动选择文件
8. 要通知用户发行版本尚不可用于生产，可能不稳定，请选择 **This is a pre-release（这是预发布）**
9. 如果您准备推广您的发行版，请单击 **Publish release（发布版本）**。 要在以后处理该发行版，请单击 **Save draft（保存草稿）**

> 你也可以随时编辑已发行的版本..
> 还可以删除他们

# 为 GitHub Pages 站点创建自定义 404 页面

创建名为 `404.md` 或 `404.html`的文件

如果将文件命名为 404.md，请将以下 YAML 前页添加到文件的开头：

```
---
permalink: /404.html
---
```

随意填充内容.之后提交即可.

# 使用 HTTPS 保护 GitHub Pages 站点

对于使用 2016 年 1 月 15 日后创建的 github.io 域的 GitHub Pages 站点，需要强制实施 HTTPS。 如果您在 2016 年 6 月 15 日之前创建了站点，则可以手动启用实施 HTTPS。

> 在 "GitHub Pages" 下，选择 Enforce HTTPS（实施 HTTPS）。

如果您对 GitHub Pages 站点启用了 HTTPS，但站点的 HTML 仍通过 HTTP 引用图像、CSS 或 JavaScript，则您的站点将提供混合内容。 提供混合内容可能会降低站点的安全性，并导致在加载资产时出现问题。

# 配置 GitHub Pages 站点的自定义域

要设置 www 或自定义子域，例如 www.example.com 或 blog.example.com，您必须在站点的仓库中创建 CNAME 文件，并使用 DNS 提供程序配置 CNAME 记录。

> 警告：我们强烈建议不要使用通配符 DNS 记录，例如 *.example.com。 通配符 DNS 记录将允许任何人在您的其中一个子域上托管 站点。

---

所以你需要创建一个`CNAME`文件,并提交到GitHub

内容大概如下
```
example.com
```

之后前往你的域名解析服务商,添加解析到你的`github.io`

然后，确保 CNAME 文件格式正确。
* CNAME 文件名必须全部大写。
* CNAME 文件只能包含一个域。 要将多个域指向您的站点，必须通过 DNS 提供程序设置重定向。
* CNAME 条目必须是裸域。 例如，www.example.com、blog.example.com 或 example.com。
* CNAME 条目只能在 GitHub 上使用一次。 例如，如果另一个仓库的 CNAME 文件包含 example.com，则不能在您仓库的 CNAME 文件中使用 example.com。

# 关于 GitHub 职位招聘

您可以在 GitHub Jobs 上发布职位招聘，为您的企业寻找人才。

您可以使用 markdown 格式化职位发布。

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->



<!-- 链接 -->

[GitHubDeskTop]:https://docs.github.com/cn/desktop "GitHubDeskTop官方文档"
[GitHubDeskTopDownLoad]:https://desktop.github.com/ "GitHubDeskTop下载"
[GitHub_Search_localhost]:GitHubDoc_搜索 "ML博客_GitHub搜索"
<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"




