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




