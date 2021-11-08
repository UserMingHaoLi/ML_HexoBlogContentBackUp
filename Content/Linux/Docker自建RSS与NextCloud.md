---
title: Docker自建RSS与NextCloud
date: 2019-10-09 20:39:00
updated: 2019-10-09 20:39:00
id: ml-20191009-203900-g9
categories:
	- 工具部署
tags: 
  - 云服务器
	- Docker
	- RSS
	- NextCloud
---
**内容引用**

> [Docker官方文档][Docker官方文档]  
> [Docker国内镜像][Docker国内镜像]  
> [博客-Docker-Compose][博客-Docker-Compose]  
> [RSSHub][RSSHub]  
> [Awesome-TTRSS][Awesome-TTRSS]  
> [腾讯云开发者实验室-nextcloud][腾讯云开发者实验室-nextcloud]

<!--more-->

# 搭建Docker 

阅读官方文档即可快速搭建

命令如果无效, 建议使用sudo权限

## 卸载旧版本

> apt-get remove docker docker-engine docker.io containerd runc

## 更新apt

> apt-get update

## 允许apt使用Https

```
apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

## 添加Docker官方密钥

> curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

其实上面这句话是下载并存储为密钥.

## 检验密钥是否完成

> apt-key fingerprint 0EBFCD88

此时显示
```
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

## 设置稳定存储库

你也可以设置测试库或者更先进的每晚库

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

## 正式安装

先更新apt

> apt-get update

之后安装

> apt-get install docker-ce docker-ce-cli containerd.io

完成之后使用hello-world进行校验

> docker run hello-world

**此时可以看到运行了官方的hello-world镜像**

# 部署Docker-Compose

docker-Compose是Docker的一个外挂组件, 通过配置文件快速运行Docker. 省去很多命令. 维护也省心.

首先前往 http://get.daocloud.io/ 查看目前最新的镜像下载地址

## 下载Docker-Compose

```
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```
## 运行

> chmod +x /usr/local/bin/docker-compose

# 部署RSSHub

参照RSSHub官网进行安装即可

## 下载源代码

**非必须**, 其实我们只是需要其中的 `docker-compose.yml` 文件

> 你可以使用任何方式将此文件移动到之前安装Docker的服务器上, 记得新建文件夹.

注意先转到RSSHub目录

```
cd /home/ubuntu  
mkdir RSSHub
```

RSSHub推荐的指令(实际我是使用ftp上传上去的.)

> wget https://raw.githubusercontent.com/DIYgod/RSSHub/master/docker-compose.yml

## 使用Redis缓存

> $ docker volume create redis-data

此命令实际上是使用Docker创建了一个Redis镜像.

## 设置参数

需要编辑之前的文件以便达到自定义的效果.

首先看下源文件

**之后根据RSSHub官网给的参数自行编辑即可**

```
CACHE_EXPIRE: '3600'
CACHE_CONTENT_EXPIRE: '14400'
LISTEN_INADDR_ANY: '3'
PORT: '1200'
```
**主要是新增了如下参数

## 启动

只需要运行以下指令即可启动RSSHub

> docker-compose up -d 

## 停止启动

docker-compose down

## 更新

使用最新的`docker-compose.yml` 文件重新配置并运行即可

## 使用

此时访问你的云服务器相应端口即可. 记得开放你服务器商的安全组相关端口

> 如果你使用以上配置, 那么端口是`1200`

# TTRSS

相信你已经通过上面的流程学会了使用Docker-compose. 那么下面就不会这么详细了.

## 下载配置文件

[docker-compose.yml](https://github.com/HenryQW/Awesome-TTRSS/blob/main/docker-compose.yml)

```
wget https://raw.githubusercontent.com/HenryQW/Awesome-TTRSS/main/docker-compose.yml
```

> 注意新建文件夹, 不同的服务要运行在不同的文件夹里面

> 如果不能创建或者传递FTP,注意可能是需要写入权限`chmod 777`

## 修改配置

按照自己的需要修改配置

修改了`数据库密码`和`实例地址(URL)`,`两个ID`,`添加额外支持的端口`  

如果是root用户,则需要`修改两个Id为0`

如果要支持1200端口,则需要使用`ALLOW_PORTS`参数

启动不了推荐使用`docker-compose down --rmi all`删除所有容器,之后重新运行

`docker-compose logs`能帮助诊断问题

## 运行

还是使用 

> docker-compose up -d 

## 访问

通过你自定义的实例地址访问, 注意实例地址的端口要和你配置的端口号一样.

> 打不开记得检查云服务器的安全组有没有开放相关端口.

# nextcloud

私有云盘是好东西, 不过一般云服务器的硬盘都很小, 所以后面会挂载一个腾讯云Cos存储(收费存储服务)

## 编写一个配置文件

```
version: '2'

services:
  db:
    image: mariadb
    restart: always
    volumes:
      - /home/ubuntu/nextcloud/db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=nextcloud
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:  
    image: nextcloud
    restart: always
    ports:
      - 1980:80
    links:
      - db
    volumes:
      - /home/ubuntu/NextCloud/www:/var/www/html
```

volumes，作用是，将宿主机的目录挂载到docker容器中，这样操作文件时，不用登录docker容器了，直接在宿主机操作就可以了

注意其中的地址 `/home/ubuntu/NextCloud` 这个是我创建的文件夹, 根据你自己的需求修改.

然后是端口`1980:80` 根据自己的需要映射.

**数据库的相关设置自行修改**

## 运行

老规矩

> docker-compose up -d 

## 使用

访问你的相关IP和端口即可.

注意, 第一次访问会写配置, 之后就只能通过这个地址访问了, 如果还想扩展安全地址, 需要修改相关配置

到你服务的目录下找到 `www/config/config.php` 进行修改即可

**修改完之后记得重新启动服务**

# 挂载腾讯云Cos到服务器

这个就相当云给你的服务器添加一个远程的硬盘,容量大, 速度取决于网速. 根据使用情况收费, 具体看文档.

挂载比较繁琐, 先看下使用到的工具吧.

[cosfs文档](https://cloud.tencent.com/document/product/436/6883)

## 获取源码

> git clone https://github.com/tencentyun/cosfs /usr/cosfs

出现`GnuTLS recv error (-110)`, 可以使用`git config --global http.postBuffer 2147480000`, 扩展下载限制

## 安装依赖

> sudo apt-get install automake autotools-dev g++ git libcurl4-gnutls-dev libfuse-dev libssl-dev libxml2-dev make pkg-config fuse

## 编译和安装

> cd /usr/cosfs  
> ./autogen.sh  
> ./configure  
> make  
> sudo make install  
> cosfs --version

## 配置密钥

> echo 'BucketName-APPID':'SecretId':'SecretKey' > /etc/passwd-cosfs

首先是 'BucketName-APPID' 这个替换为你存储桶的名称加桶的ID.

例如 `mingcheng-123456789` 这样

后面的 'SecretId' 和 'SecretKey' 分别是你的API访问密钥ID和Key. 用来使用你的存储API. 没有可能需要申请一个. 由于存储是明文的, 建议使用子账号, 只给Cos相关的权限, 否则后果自负.

联合起来可以举一个例子

> echo mingcheng-123456789:nidemiyaoid:nidemiyaomima > /etc/passwd-cosfs

这句话就是写入一个字符串到相关位置.

## 运行

> cosfs 'BucketName-APPID' 'MountPoint' -ourl='CosDomainName' -odbglevel=info

首先 'MountPoint' 是要挂在那个目录, 是你本地的目录地址.

> 注意只能放在你配置文件目录的 `www/`文件夹以下. 不然后面无法通过nextcloud访问.

然后 'CosDomainName' 是你的桶地区代号

例如 `http://cos.ap-beijing.myqcloud.com` 这个可以在官网查到

**注意后面还有可选参数** 比如我自己就带了的 `-oallow_other` 全用户可访问

> df -h

上面这句查看磁盘, 看到你指定的文件夹变成一个256T的磁盘就表示成功了.

## 挂载到nextcloud

NextCloud网站, 选中右上角的应用, 启用最下面的外部扩展插件

之后到设置, 外部扩展进行挂载操作, 挂载类型是`本地`. 

**路径是 /var/www/目录** 这个www实际上就是Docker映射过来的.

你在机器里面看不到, 如果你直接设置 `/var`作为外挂目录就可以看到多了一个`www/`目录. 这就到你的服务里面了.

# 完毕

谢谢阅读.

[Docker官方文档]:https://docs.docker.com/install/linux/docker-ce/ubuntu/ "ubuntu"

[Docker国内镜像]:https://blog.csdn.net/hujyhfwfh2/article/details/83934415 "镜像"

[博客-Docker-Compose]:https://blog.csdn.net/hujyhfwfh2/article/details/83934415 "blog"

[RSSHub]:https://github.com/DIYgod/RSSHubv "RSSHub"

[Awesome-TTRSS]:https://ttrss.henry.wang/ "ttrss"

[腾讯云开发者实验室-nextcloud]:https://cloud.tencent.com/developer/labs/lab/10381