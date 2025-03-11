---
title: hexo模板
date: 2021-03-12 22:52:00
updated: 2023-07-11 00:35:00
id: ml-20250311-221500-g200
categories:
	- AI
tags: 
	- AI
---



# 服务器稳定性方面参考

* 组装
* 维修
* 电源 + 备用电源
* 停电保护磁盘
* ECC纠错内存
* 散热(防止高温宕机)
  * 风道
  * 空调
* 网络
  * 如对外提供服务需要稳定可达
* 技术人员上门


<!--more-->

时间 `20250311-221500`

# 结论

按中等规格配置主板,内存,硬盘,电源,散热后

以下不含显卡

* 商用服务器平台. 整机价格约3W+
    * 商用服务器可托管到机房, 价格另算
    * 商用服务器与工作站本质无差别, 价差在组装,维护,技术人员上门成本
* 工作站级别, 整机价格约1.5w+
  * 二手平台捡现成的工作站, 整机价格约9k+(难以鉴定完整性)
* 个人电脑, 整机价格约7k
  * 空间不足可购置显卡延长线, 对于AI训练损失微小
  * 最多支持两卡

> 安装在公司内还需考虑空调效率, 风道, 网络, 供电稳定等问题..

额外方案: 可联系线下PC装机店咨询现成的方案, 本地应该有类似服务商

# 网络参考

> 目前支持多卡算例的CPU紧缺, 已不存在所谓洋垃圾(降低性能, 得不偿失)


双卡共计 48GB 显存
* 推理可采用 4bit 量化方案时运行到 70B 级别的模型
* 34B 左右的模型进行微调
* 整机预算在4w元人民币
  * 不含显卡为1.5w
  
![][网络来源方案_配置图]
原文链接: https://zhuanlan.zhihu.com/p/16864316403




# 消费级主板

![][个人主板外观]
![][个人主板配置]

也可以插2卡
* 主板拥有2条PCIE4.0 * 16以上通道
* CPU拥有至少2条PCIE*16通道的

> 主板价格一般在2k+

# 个人工作站级主板

网上查阅到有人组装过 z790主板,14900k的cpu,延长线接2台4090

![][网络来源方案]

**自行配置服务器级别磁盘, 内存, 散热, 自行组装**  
**稳定性与周边配置相关, 出问题自行维护**


# 商用服务器平台(服务器主板)


![][商业服务器配置]

![][商业服务器报价]

提供组装, 保修服务

价格从2w起, 不封顶

**稳定性与云服务相当, 但是放到自己机房可能会受到环境影响**  
**比如网络, 电源, 外部散热(空调效率)**

> 一般也可以托管到服务器出售商的机房, 额外付费




# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

[商业服务器配置]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E5%95%86%E4%B8%9A%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE.png "商业服务器配置"

[商业服务器报价]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E5%95%86%E4%B8%9A%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%8A%A5%E4%BB%B7.png "商业服务器报价"

[个人主板外观]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E4%B8%AA%E4%BA%BA%E4%B8%BB%E6%9D%BF%E5%A4%96%E8%A7%82.png "个人主板外观"

[个人主板配置]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E4%B8%AA%E4%BA%BA%E4%B8%BB%E6%9D%BF%E9%85%8D%E7%BD%AE.png "个人主板配置"

[网络来源方案]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E7%BD%91%E7%BB%9C%E6%9D%A5%E6%BA%90%E6%96%B9%E6%A1%88.png "网络来源方案"

[网络来源方案_配置图]:https://raw.githubusercontent.com/UserMingHaoLi/ML_HexoBlogContentImages/refs/heads/main/Content/AI/%E5%A4%9A%E6%98%BE%E5%8D%A1AI%E5%B7%A5%E4%BD%9C%E7%AB%99%E7%BB%84%E8%A3%85%E8%B0%83%E7%A0%94/%E7%BD%91%E7%BB%9C%E6%9D%A5%E6%BA%90%E6%96%B9%E6%A1%88_%E9%85%8D%E7%BD%AE%E5%9B%BE.png "网络来源方案_配置图"



<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
