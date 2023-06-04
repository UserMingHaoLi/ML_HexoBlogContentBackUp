---
title: Unity模块下载_离线使用
date: 2023-06-03 09:49:00
updated: 2023-06-03 09:49:00
id: ml-20230603-094900-g188
categories:
	- Unity
	- 离线
tags: 
	- Unity
	- 离线
---


目前过多依赖UnityHub, 但是难免有离线开发需求

<!--more-->


# 下载Unity离线模块

一个例子链接如下  
`https://download.unitychina.cn/download_unity/ffc62b691db5/TargetSupportInstaller/UnitySetup-Windows-IL2CPP-Support-for-Editor-2019.4.40f1.exe`

其中`https://download.unitychina.cn/download_unity/ffc62b691db5/`  
可通过官网下载指定版本得到

合适的组件有
```
Windows64EditorInstaller/UnitySetup64.exe

TargetSupportInstaller/UnitySetup-Android-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-AppleTV-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-iOS-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Linux-IL2CPP-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Linux-Mono-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Lumin-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Mac-Mono-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Universal-Windows-Platform-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-WebGL-Support-for-Editor-2019.4.40f1.exe

TargetSupportInstaller/UnitySetup-Windows-IL2CPP-Support-for-Editor-2019.4.40f1.exe

MacEditorInstaller/Unity.pkg

MacEditorTargetInstaller/UnitySetup-Android-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-AppleTV-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-iOS-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-Linux-IL2CPP-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-Linux-Mono-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-Lumin-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-Mac-IL2CPP-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-WebGL-Support-for-Editor-2019.4.40f1.pkg

MacEditorTargetInstaller/UnitySetup-Windows-Mono-Support-for-Editor-2019.4.40f1.pkg
```

# Unity离线安装PackageManager

查阅文档[包管理器](https://docs.unity3d.com/cn/current/Manual/Packages.html)

其内有本地安装, 部署内网包缓存等方法

Unity 打开项目时，Unity Package Manager 会读取`项目清单文件 (1)` 以确定要在项目中加载的包。然后会向每个包（在清单中显示为依赖项）的`包注册表服务器 (2)` 发送一个请求

1.  Unity 项目根文件夹下的 Packages 文件夹中找到名为 `manifest.json` 的项目清单文件
2.  范围注册表允许 Unity 将任何自定义包注册表服务器的位置传达给 Package Manager，以便用户可以同时访问多个包集合
    1.  在 Unity 的 Package Manager 窗口中，当您选择 `Unity Registry` 上下文时，所有在 Unity 注册表中注册的包都出现在列表视图中
    2.  项目清单使用一个 `scopedRegistries` 属性，其中包含一个范围注册表配置对象数组

# VisualStudio本体和工作负载离线安装

查阅文档[Visual Studio 管理员指南](https://learn.microsoft.com/en-us/visualstudio/install/create-a-network-installation-of-visual-studio?view=vs-2022)

其内有本体及其工作负载的离线安装方式



# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
