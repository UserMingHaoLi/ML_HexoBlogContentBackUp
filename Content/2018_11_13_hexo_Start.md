---
title: 无服务器搭建Hexo私人博客
date: 2018-11-30 23:49:00
updated: 2018-11-30 23:49:00
categories:
	- hexo
tags: 
	- hexo
---
# Hexo+Github个人博客
无需服务器和域名,使用github.io域名. hexo上传静态网页.

[hexo官网][hexoio]

[引用_搭建博客_知乎][hexo&github]

<!--more-->
1. ### 前言: 你可以免费使用腾讯云实验室搭建Hexo, 满不满意试试再看.

>https://cloud.tencent.com/developer/labs/search?keyword=hexo

1. ### 首先注册 <https://github.com/>.
注意了,此时的username就是你到时候博客的二级域名
    
        username.github.io

2. ### 然后需要安装环境

[nodejs][nodejshttp]

[git][gitcn]

3. ### 之后打开git命令窗口 输入
   
        $ npm install -g hexo-cli

4. ### 打开cmd命令窗口

        hexo init blog

5. ### 看到以下代码表示成功了

        INFO Start blogging with Hexo!

6. ### 之后使用简写命令部署hexo
   
    $ hexo generate

        hexo g

    $ hexo server

        hexo s

7. ### 之后访问hexo本地即可
   
    [localhost:4000][hexolocl]

8. ### cmd输入

        ssh-keygen -t rsa -C "Github的注册邮箱地址" 

    一路Enter过来

1. ### 得到信息
    
        Your public key has been saved in /c/Users/user/.ssh/id_rsa.pub.

    找到该文件，打开，Ctrl + a复制里面的所有内容，然后进入

    [git keys](https://github.com/settings/keys)

    New SSH key

    键入即可

2.  ### blog目录下面_config.yml
    
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

3.  特别提醒，在每个参数的：后都要加一个空格
    
修改网站相关信息

        title: 博客名
        subtitle: 副标题
        description: 网页描述SEO用途(github不让百度爬,所有基本没用)
        author: youname
        language: zh-CN
        timezone: 时区

12. ### 部署配置

        deploy: 
            type: git
            repo: https://github.com/"你github的username"/"你github的username".github.io.git
            #示例 https://github.com/myname/myname.github.io.git
            branch: master

13. ### gitcmd键入

        $ hexo new "newhelloworld"

 #### 查看文章  
        title: 测试文章
        date: 2018-11-13 23:03:44
        tags: 
            - 测试
            - hello,world

14. ### gitcmd
    
        $ hexo clean
        $ hexo g
        $ hexo s

之后发现文章发布成功

15. ### gitcmd 部署

        $ hexo deploy

    也可以简写

        $ hexo d

16. ### 之后你可以访问youname.github.io查看你的博客

17. 网上搜索主体美化你的博客 我使用next

[hexo&github]: https://zhuanlan.zhihu.com/p/25729240 "知乎"

[nodejshttp]:https://nodejs.org/en/ "nodejs官网"

[gitcn]:https://github.com/waylau/git-for-win "git国内快捷下载"

[hexolocl]:http://localhost:4000/ "本地预览hexo"

[hexoio]:https://hexo.io/  "hexo.io"