---
title: 疾风计划_Main
date: 2021-11-18 01:07:00
updated: 2021-11-18 01:07:00
id: ml-20211118-010700-g170
categories:
	- 基础
tags: 
	- 基础
---

![学习路线图][路线图]

![学习课程表][课程表]

> 基础补充,不多说.

<!--more-->

# 计算机文化基础

IT行业必须不断进步,不进则退

IT行业必须拥抱创新,不然增量不足,到达上限之后无法获得新的增量.  
遇到问题无法给出解决方案.

了解信息技术, 最好的凡是就是实践,去做.

采用数字装置的主要原因是可精确复制特性

二进制的变化可用`与或非`三种表示完全,且这总原件易于制造.

二进制转十进制  
小数点前或者整数要从右到左用二进制的每个数去乘以2的相应次方并递增,小数点后则是从左往右乘以二的相应负次方并递减.   
例如:二进制数1101.01转化成十进制  
`1101.01（2）=1*20+0*21+1*22+1*23 +0*2-1+1*2-2=1+0+4+8+0+0.25=13.25（10）`

十进制整数转换为二进制整数  
除2取余,逆序排列  
```
如:255=（11111111）
255/2=127=====余1
127/2=63======余1
63/2=31=======余1
31/2=15=======余1
15/2=7========余1
7/2=3=========余1
3/2=1=========余1
1/2=0=========余1
```

乘2取整,顺序排列  
用2乘十进制小数,可以得到积,将积的整数部分取出,再用2乘余下的小数部分,又得到一个积  
达到所要求的精度为止
```
如:0.625=（0.101）
0.625*2=1.25======取出整数部分1
0.25*2=0.5========取出整数部分0
0.5*2=1==========取出整数部分1
```

## TODO

加法器,继电器,电子管,晶体管,集成电路

IEEE754 浮点
补码,反码,模数,补数
GB18030


# C++语言程序设计基础

# C++语言程序设计进阶

# 程序设计基础

# 计算机程序设计基础

# 面向对象程序设计(C++)

# 离散数学

# 概率论于数理统计

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

[路线图]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/%E8%AE%A1%E7%AE%97%E6%9C%BA_%E7%96%BE%E9%A3%8E%E8%AE%A1%E5%88%92_%E5%9F%BA%E7%A1%80%E8%A1%A5%E5%AE%8C/%E8%AE%A1%E7%AE%97%E6%9C%BA_%E7%96%BE%E9%A3%8E%E8%AE%A1%E5%88%92_%E5%9F%BA%E7%A1%80%E8%A1%A5%E5%AE%8C_%E8%B7%AF%E7%BA%BF%E5%9B%BE.png?raw=true "路线图"

[课程表]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/%E8%AE%A1%E7%AE%97%E6%9C%BA_%E7%96%BE%E9%A3%8E%E8%AE%A1%E5%88%92_%E5%9F%BA%E7%A1%80%E8%A1%A5%E5%AE%8C/%E8%AE%A1%E7%AE%97%E6%9C%BA_%E7%96%BE%E9%A3%8E%E8%AE%A1%E5%88%92_%E5%9F%BA%E7%A1%80%E8%A1%A5%E5%AE%8C_%E8%AF%BE%E7%A8%8B%E8%A1%A8.jpg?raw=true "课程表"

<!-- 链接 -->


<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"


