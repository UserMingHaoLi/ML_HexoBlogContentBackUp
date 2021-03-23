---
title: Hexo部署Github个人博客
date: 2021-03-15 22:03:00
updated: 2021-03-15 22:03:00
categories:
	- 工具部署
tags: 
	- Hexo
---

无需服务器和域名,使用github.io域名. hexo上传静态网页.

> 你可以免费使用腾讯云实验室搭建Hexo, 满不满意试试再看.[Hexo_腾讯云实验室][hexoioAndTencent_Link]

<!--more-->

[hexo官网][hexoio_Link]

[引用_搭建博客_知乎][Hexo&zhihu_Link]

# 注册GitHub

首先注册 [GitHub][GitHub_Link]  
注意了,此时的 `username` 就是你到时候博客的二级域名
    
**[username].github.io**

# 安装环境

[nodejs][nodejshttp]

[git][gitcn]

**注意安装nodejs可能需要勾选以下最后的勾.**

# 安装Hexo

找到一个适合安装Hexo的空文件夹,之后的操作都是再此基础上完成的

1. 之后打开git命令窗口 输入
	* `npm install -g hexo-cli`
	* 等待安装完成
2. 打开cmd命令窗口输入
   * `hexo init blog`
   * 看到以下代码表示成功了
   * `INFO Start blogging with Hexo!`

*此时,Hexo已经安装完毕,安装的文件夹Blog应该都可以看到了吧*

# 部署Hexo到本地

由于Hexo里面自带了一个 `HelloWorld.md` 文档.所以现在就可以尝试生成并部署到本地了.

> 所有文章存储于 `blog\source\_posts\` 文件夹中,你可以参阅官方文档来生成新文章,或者直接自己手写 `.md` 文件之后导入,都行.

**进入到Blog文件夹内**

CMD窗口输入 `hexo generate` 也可以输入简化版本 `hexo g`. 用于生成文件

生成完毕之后再输入 `hexo server` 或简化版本 `hexo s` 拉起本地服务器

# 查看本地服务

[localhost:4000][hexolocl] 访问本地服务器.此时就可以看到Hexo默认样式和 `HelloWorld` 文章了.

# 部署到GitHub.io

cmd输入 `ssh-keygen -t rsa -C "Github的注册邮箱地址"`  
一路Enter过来,得到信息 `Your public key has been saved in /c/Users/user/.ssh/id_rsa.pub.`  
找到这个地址,文本编辑器打开,全选复制其中内容.

然后进入[git keys][Git_Keys_Link].  
点击 `New SSH key` 输入之前复制的内容到.  
此时还可为这个Key取一个名字.方便以后查阅.  

*部署先告一段落,此时要修改Config.yml配置了*

# 修改Hexo配置

打开blog目录下面`_config.yml`文件.其内容大约如下(版本不同可能细节不太一样.)

	# Hexo Configuration
	## Docs: http://hexo.io/docs/configuration.html
	## Source: https://github.com/hexojs/hexo/
	# Site #站点信息
	title:  #标题
	subtitle:  #副标题
	description:  #站点描述，给搜索引擎看的
	author:  #作者
	email:  #电子邮箱
	language: zh-CN #语言
	# URL #链接格式
	url:  #网址
	root: / #根目录
	permalink: :year/:month/:day/:title/ #文章的链接格式
	tag_dir: tags #标签目录
	archive_dir: archives #存档目录
	category_dir: categories #分类目录
	code_dir: downloads/code
	permalink_defaults:
	# Directory #目录
	source_dir: source #源文件目录
	public_dir: public #生成的网页文件目录
	# Writing #写作
	new_post_name: :title.md #新文章标题
	default_layout: post #默认的模板，包括 post、page、photo、draft（文章、页面、照片、草稿）
	titlecase: false #标题转换成大写
	external_link: true #在新选项卡中打开连接
	filename_case: 0
	render_drafts: false
	post_asset_folder: false
	relative_link: false
	highlight: #语法高亮
	enable: true #是否启用
	line_number: true #显示行号
	tab_replace:
	# Category & Tag #分类和标签
	default_category: uncategorized #默认分类
	category_map:
	tag_map:
	# Archives
	2: 开启分页
	1: 禁用分页
	0: 全部禁用
	archive: 2
	category: 2
	tag: 2
	# Server #本地服务器
	port: 4000 #端口号
	server_ip: localhost #IP 地址
	logger: false
	logger_format: dev
	# Date / Time format #日期时间格式
	date_format: YYYY-MM-DD #参考http://momentjs.com/docs/#/displaying/format/
	time_format: H:mm:ss
	# Pagination #分页
	per_page: 10 #每页文章数，设置成 0 禁用分页
	pagination_dir: page
	# Disqus #Disqus评论，替换为多说
	disqus_shortname:
	# Extensions #拓展插件
	theme: landscape-plus #主题
	exclude_generator:
	plugins: #插件，例如生成 RSS 和站点地图的
	- hexo-generator-feed
	- hexo-generator-sitemap
	# Deployment #部署，将 lmintlcx 改成用户名
	deploy:
	type: git
	repo: 刚刚github创库地址.git
	branch: master

> **特别提醒，在每个参数的`：`后都要加一个空格**

*建议此次修改以下信息即可,后续信息可以搭建完毕后重新修改(需重启Hexo服务)*

修改网站相关信息

	title: 博客名
	subtitle: 副标题
	description: 网页描述SEO用途(github不让百度爬,所有基本没用)
	author: youname
	language: zh-CN
	timezone: 时区

之后进行部署配置

	deploy: 
		type: git
		repo: https://github.com/[你github的username]/[你github的username].github.io.git
		#示例 https://github.com/myname/myname.github.io.git
		branch: master

# 继续部署到GitHub.io

配置完成后先启动以下Hexo.看下有没有问题.

`hexo clean` 清理

`hexo g` 生成内容

`hexo s` 拉起服务器

之后还是访问 [localhost:4000][hexolocl] 本地服务器. 查看是否正常显示.

**推送内容到GitHub.io**

和上面一样,输入命令 `hexo deploy` 或者简写 `hexo d` 即可.

完成之后就可以访问 `[你github的username].github.io` 来查看你的博客了.

# 可能的问题

1. 在执行 `hexo deploy` 后,出现 `error deployer not found:github` 的错误
   * 可能需要安装 `npm install hexo-deployer-git --save` 
2. git提示 `unable to auto-detect email address`
   * 可能需要输入邮箱和用户名
   * `git config --global user.email "[[你github的注册邮箱]"`
   * `git config --global user.name "[你github的username]"`
3. 需要输入`GitHub access tokens`
   * 前往 [GitHub Access Tokens][Git_Access_Tokens_Link] 注册一个新的AccessToken即可
   * 权限可选如下 `repo`.全部 `admin:repo_hook`.全部
   * 或者全部勾选,主账号登录,随你自己.

# 美化

网上搜索 `Hexo美化`, `hexo主题`, `Hexo themes`, 都可以收获很多内容,这里不细讲了


# 使用Next主题

> [Hexo_Next主题(停止维护)][Hexo_Next]  
> [Hexo_Next主题(社区维护)][Hexo_Next_New] 

补一个[Hexo大全][Hexo_All_Link]

首先下载Hexo. 需要保证进入Hexo文件夹.
这一目录中应当有 `node_modules`、`source`、`themes` 等若干子目录

然后使用如下命令,这将Clone整个GitHub库到 `themes/next` 文件夹

```
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

然后在站点的配置文件 `_config.yml` 中找到 `theme` 后添加：

```yml
theme: next  # 配置成刚下载的next主题
```
*之后等待下载完毕即可*

# 配置Next

进入下载的 `next` 文件夹内, 需要设置 `_config.yml`.

> 此时为 `next` 的 `_config.yml` ,而不是 `hexo` 的 `_config.yml`

打开其中的这一块

```
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  sitemap: /sitemap.xml || sitemap
  commonweal: /404/ || heartbeat

```

按需修改即可

*我就打开了 `tags` 和 `categories`*

之后要手动生成这些页面

## 新建一个 tags 页面

使用命令

```
hexo new page tags
```

修改创建出来的文件 `hexo/source/tags/index.md` 内容如下

```
---
date: 2021-03-15 22:03:00   # 时间随意
type: "tags"                # 类型一定要为tags
---
```

## 新建一个 categories 页面

基本是和上面相同的, 就是 `tags` 替换为了 `categories`

> 吐槽以下, 实际上Hexo已经制动帮你创建好了
> 只需要添加 `type: "tags"` 即可 `categories` 同理

> 先到此为止, 后面可能开以下搜索和评论.

# 为归档页面设置内容

在Hexo设置中添加如下代码
```yml
archive_generator:
  per_page: 50
  <!-- yearly: true
  monthly: true -->
```
>建议跟在`index_generator`块后面

# 查看

直接 `clean` `g` `s` 三连查看

没问题就 `d` 部署.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->


<!-- 链接 -->

[HexoioAndTencent_Link]:https://cloud.tencent.com/developer/labs/search?keyword=hexo "hexo 腾讯云实验室"
[Hexoio_Link]:https://hexo.io/  "hexo.io"
[Hexo&zhihu_Link]:https://zhuanlan.zhihu.com/p/25729240 "知乎"
[GitHub_Link]:https://github.com/ "GitHub 官网"
[nodejshttp]:https://nodejs.org/en/ "nodejs官网"
[gitcn]:https://github.com/waylau/git-for-win "git国内快捷下载"
[hexolocl]:http://localhost:4000/ "本地预览hexo"
[Git_Keys_Link]:https://github.com/settings/keys "GitHub Keys编辑"
[Git_Access_Tokens_Link]:https://github.com/settings/tokens "GitHub Access_Tokens编辑"
[Hexo_Next]:https://github.com/iissnan/hexo-theme-next/blob/master/README.cn.md "HexoNext主题中文文档"
[Hexo_Next_New]:https://github.com/theme-next/hexo-theme-next "HexoNext主题中文文档"
[Hexo_All_Link]:https://tding.top/archives/aad98408.html "Hexo博客搭建大全"
<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
