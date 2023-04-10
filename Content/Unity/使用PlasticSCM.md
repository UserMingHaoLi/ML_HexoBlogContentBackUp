---
title: 使用PlasticSCM
date: 2021-11-14 06:14:00
updated: 2021-11-14 06:14:00
id: ml-20211114-061400-g168
categories:
	- Unity
	- 工具使用
tags: 
	- Unity
---

`PlasticSCM`是Unity推出的版本管理工具,类似于Git或SVN.  
当然,功能上有些许区别.

[PlasticHub地址][plastichub_Link]   
[Unity官网Plastic页][Unity_Plastic_Link]  

> PlasticHub中Unity官方账号为**Unity-Tech-Cn**

<!--more-->

# 引子

[活动介绍][PlasticSCM_learn_Link]  
[作业征集][plastichub_learnActivity_Link]

将课程作业提交到 PlasticHub 上,就能获得 Unity 中文课堂价值 50 元代金券。

成功提交作业的同学,可以在 Unity 社区里分享作业心得,并把心得链接发在 PlasticHub 活动作业征集贴中。这次有 6 门课,每门课选 6 位优秀作品,送经典款 Logo 周边。

* 提交作业步骤如下

使用 PlasticSCM 提交作业时,记得在提交信息中使用如下格式
```
[user comments] + LearnId:[中文课堂用户24位learnId]
```
例
```
PlasticHub Homework LearnId:123456789012345678901234
```

* 申请周边步骤如下

大家完成中文课堂(learn.u3d.cn)如下6门课程后,将使用心得和经验总结成博客文章发表在官方社区中。并将文章链接,截图以及标题回复到该问题后面

回复格式参考：  
[文章标题]  
[链接]  
[截图]  
例如： 通过Addressable + CCD实现资源更新流程  
https://developer.unity.cn/projects/ccd-workshop  
[截图]    

# 学习课程

[课程地址][learn_PlasticSCM_Link]

作业如下
```
在分支查看器中创建新分支,改变卡丁车的材质后,将改动上传到PlasticSCM仓库中,最后将该任务分支合并到主线分支。
项目地址：https://plastichub.unity.cn/unity-tech-cn/PlasticTutorial_Karting
贴心提示：将项目Fork(派生)到自己组织下完成提交
```

于是,我按照作业要求,将内容`Fork`到自己的组织下,并使用`UnityHub`下载打开

# 修改设置

我注意到`PlasticHub`是一个独立于`Unity`之外的网站,且会依据`Unity`组织注册一个新账号.  

那么第一件事就是看下设置

推荐进行如下修改  
* 勾选`隐藏电子邮件地址`
* 勾选`隐藏个人资料页面中的活动`

注意,如果只勾选以上两项,是无法修改成功的,有一个报错`Sensitive word check failed, please try again.(敏感字检查失败,请重试)`  
但是我甚至都没打字,只是进行了勾选, 推测是将空行识别为敏感字符, 所以额外填写了空着的三行  
最后竟然告诉我等待**人工审核**...行吧

# 开启项目

然后打开之后报了些错`999+`, 拉到最上面就看到两条,关键字是  
```
Multiple precompiled assemblies with the same name unityplastic.dll included or the current platform. Only one assembly with the same name is allowed per platform.
```
然后两个地址分别指向`collab-proxy`,`Plastic SCM`  

其中`collab-proxy`官方文档中说*Collaborate 是一个供团队保存、共享和同步 Unity 项目的简单工具*  
但是网上搜索结果全是报错,然后教你如何删除的..., 完全不明白怎么使用.反正先把它删了.

* 打开 `window -> Package Manager`, 搜索`collab-proxy`, 然后`Remove`删除它  

但是`Plastic SCM`是本次要使用的版本管理工具,不能删除,所以尝试升级到最新版本.  
* `Package Manager`中搜索`Plastic SCM`,`Update`将其升级到最新版本  
之后报错就消失了,`Plastic SCM`界面也可以正常显示了

# 使用PlasticSCM

由于之前修改了一些`Package Manager`,导致已经可以上传修改了,首先右键点击任何修改,选择`Diff`来比对修改内容

然后我就`Checkin Changes`上传了内容

在`Unity`的`PlasticSCM`页中,点击右上角的`Launch Plastic`可打开PlasticSCM应用,选中左侧的`分支资源管理器`可查看版本的可视化列表.  
可见我刚刚的提交是在`main`上进行的,这不是很好,先在需要创建一个功能分支, 右键当前版本的Icon(带小房子图标的那个), 选则`从此变更集创建分支`, 名称为`合并测试`

首先,我得直到要修改什么, 选中项目中的`Scenes`文件夹,发现其中的`SampleScene`场景只是一个空场景, 于是转向`Karting/Scenes`文件夹,使用其中的`LoseScene`场景开始游戏.  
现在,就可以看到作业目标*卡丁车*了

其预制体名为`KartClassic_Player`,要修改的车体在`KartVisual/Kart/Kart_Body`, 材质名称为`KartBody`.  
右键此材质,选择`Select Material`来在Unity中前往其文件夹  

随便改改,保存预制体.

然后通过`PlasticSCM`进行提交

此时先不合并,再创建一个*冲突测试*分支.  
还是同样的操作,随便改改材质,保存Prefab,提交  

此时前往可视化界面,准备将这两个分支合并到主干`main`,先合并`合并测试`分支  
右键点击,选择`将此变更集合并到`,目标选择为`main`, 刚好,我们之前以为的`main`,其实是`main/用户名`, 所以这次创建的新`main`正好作为默认分支.

然后将`冲突测试`合并到main,果然有冲突.  

右键冲突文件,选择`选择方法合并所有文件`, 如果按照视屏中选择`合并时自动冲突解决`,那么会默认丢弃此更高,所以本地选择`合并时手动冲突解决`

手动解决完成,可以合并

切换工作区到`main`的最后一个节点,可以看到,所有修改都已正确应用.

最后,提交作业,方法在文章最前面.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->


<!-- 链接 -->

[plastichub_Link]:https://plastichub.unity.cn/ "plastichub"
[Unity_Plastic_Link]:https://unity.cn/plasticscm "PlasticSCM (Unity项目版本控制系统)"
[PlasticSCM_learn_Link]:https://developer.unity.cn/projects/616fcf53edbc2a0021a91a35 "PlasticSCM 对应的云端项目托管协作平台联合Unity 中文课堂送福利啦！"
[plastichub_learnActivity_Link]:https://developer.unity.cn/ask/question/616d3238edbc2a34911c6381 "PlasticHub上线活动——作业征集"
[learn_PlasticSCM_Link]:https://learn.u3d.cn/tutorial/unityzui-xin-ban-ben-kong-zhi-xi-tong-plastic-scmxi-lie-ke "Unity最新版本控制系统Plastic SCM系列课"


<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"


