---
title: hexo模板
date: 2021-03-12 22:52:00
updated: 2021-08-06 00:35:00
id: ml-20210415-213300-g1
categories:
	- Hexo
tags: 
	- Hexo
---


# hexo模板moreUp


<!--more-->

![UML类图模板][UMLClassMode]

<!-- 
id: ml-20210808-014200-g151
每次使用自觉+1.
-->

放点小技巧在这里

1. 快捷方式直接打开Unity项目
* 编辑快捷方式，在目标后面加空格` –projectPath 你的项目地址(C:\a这样)`

*摸了一个快排*
```C#
static void Main(string[] args)
{
	int min = -100000;
	int max = 100000;
	int forCount = 10000;
	int randCount = 10000;

	Random random = new Random();
	List<int> values = new List<int>();

	for (int count = 0; count < forCount; count++)
	{
		values.Clear();
		for (int i = 0; i < randCount; i++)
		{
			int tmp = random.Next(min, max);
			values.Add(tmp);
		}
		quickSort(values);//快排,上面是出题,下面是验证.

		int pre = min;
		foreach (var item in values)
		{
			if (item < pre)
				throw new Exception();
			pre = item;
		}
		if(count % (forCount / 100) == 0)
			Console.WriteLine($"Count = {count}");
	}
	Console.WriteLine("OK!");
	Console.ReadLine();
}

static void quickSort(List<int> num)
{
	quickSort_Base(num, 0, num.Count - 1);
}

static void quickSort_Base(List<int> num, int l, int r)
{
	if (l >= r) return;
	int value = num[l];
	int left = l;
	int right = r;
	while (left < right)
	{
		while (left < right && num[right] >= value)
		{
			right--;
		}
		Swap(num, left, right);
		while (left < right && num[left] <= value)
		{
			left++;
		}
		Swap(num, left, right);
	}
	quickSort_Base(num, l, left-1);
	quickSort_Base(num, right+1, r);
}
static void Swap(List<int> num, int l, int r)
{
	if (l == r) return;
	num[l] ^= num[r];
	num[r] ^= num[l];
	num[l] ^= num[r];
}
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
[UMLClassMode]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/%E6%A8%A1%E6%9D%BF/UML%E7%B1%BB%E5%9B%BE%E6%A8%A1%E6%9D%BF.png "UML类图模板"
<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
