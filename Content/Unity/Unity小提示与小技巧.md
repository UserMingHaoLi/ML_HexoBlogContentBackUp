---
title: Unity小提示与小技巧
date: 2021-11-17 02:05:00
updated: 2021-11-17 02:05:00
id: ml-20211117-020500-g169
categories:
	- Unity
	- 小技巧
tags: 
	- Unity
---



<!--more-->

# 快捷键工具

使用`ShortcutsManager`查看,编辑,管理Unity快捷键

* `Hierarchy`中 *Alt+鼠标点击* 可快速打开,关闭所有的层

# PlaymodeTint

运行模式变色,用于提示当前处于运行状态

# 对其

UnitSnapping,单位**增量快速对齐**

`Preferences->Colors->PlaymodeTint`,按住Ctrl+常规移动,旋转可使增量变为固定值.

修改此值可以前往`Edit->GridandSnap`

网格快速对齐`Align Selection to Grid`

除了在UI上直接点击应用对齐外,还可以在使用旋转缩放工具,且属于`Global`情况下,手动开启网格对齐模式.

> 网格的显示开关已移动至一级界面,就在和灯光,2D/3D同层的地方

> 上面提到的Color设置,也可以变更网格颜色,实际上还能变更更多颜色

***顶点快速对齐**VertexSnapping

关闭自动网格对清,并在移动物体时按住V不放,UI界面会有变化(坐标轴被移动至定点上)提示开启了顶点快速对齐

**表面快速对齐**SurfaceSnapping

实际是碰撞体贴合,所以需要有碰撞体, 同时按住Ctrl+Shift,然后移动物体,此时坐标轴变为一个方框

> Pivot使轴心贴合表面,Center使表面贴合表面

# 自定义工作区

* 停靠窗口
* 同样的窗口打开多个
* 锁定窗口(经常用作多选拖拽赋值时)
* 释放窗口,使其自由移动

# Unity中搜索

* `Hierarchy`可直接搜索脚本名称(全称),即可展示所有携带该脚本的物体

* 还可以使用Tag查抄,或label查找,其中label可自己新增和指定

* 还有SaveSearch,搜查常用查找条件,下次直接查找

* Unity内可以直接查找AssetStore

* 为游戏中的物体指定Icon,Scene中查找更方便
* `Hierarchy`中每个物体点左边的眼睛,点击可使其在场景中隐藏或显示,但只针对于显示,且并非Active.用于编辑器下选中其后面的物体.
* 另一个箭头,则是是否能在场景中选中

> 直接安装`QuickSearch`快速搜索包`Alt+'`可直接呼出或安装

# 场景视图锁定对象 Lock view to selected

选中一个物体  
`Editor-> Lock view to selected`或`Shift+F`   
此时镜头就锁定在刚刚选择的物体上了

## 摄像机摆位捷径

`Editor-> Align With View` 或`Ctrl+Shift+F` 使镜头移动到Scene当前的位置  

`Editor-> Align With View Selected` 使Scene移动到选中物体当前的位置

# 查看符合版本的参考资料

在组件旁边点击`?`可以跳转到该版本的文档

从`Package Manager`的包中,可以直接`View Documention`游览符合版本的文档

# 搜索于提问

别人怎么提问,学习提问技巧更方便搜索,或向别人提问.

提供更多信息方便别人直接给出解答.

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


