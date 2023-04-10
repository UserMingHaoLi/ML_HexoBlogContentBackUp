---
title: CSharp编程指南_访问关键字
date: 2021-04-25 22:09:00
updated: 2021-04-25 22:09:00
id: ml-20210425-220900-g130
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

就是`base`和`this`关键字

<!--more-->

# base

`base` 关键字用于从派生类中访问基类的成员
* 调用基类上已被其他方法重写的方法.
* 指定创建派生类实例时应调用的基类构造函数.

所访问的基类是类声明中指定的基类. 例如,如果指定 `class ClassB : ClassA`,则从 `ClassB` 访问 `ClassA` 的成员,而不考虑 `ClassA` 的基类.

```CSharp
public class Person
{
    protected string ssn = "444-55-6666";
    protected string name = "John L. Malgraine";

    public virtual void GetInfo()
    {
        Console.WriteLine("Name: {0}", name);
        Console.WriteLine("SSN: {0}", ssn);
    }
}
class Employee : Person
{
    public string id = "ABC567EFG";
    public override void GetInfo()
    {
        // Calling the base class GetInfo method:
        base.GetInfo();//调用`Person`基类的`GetInfo()`方法
        Console.WriteLine("Employee ID: {0}", id);
    }
}
class TestClass
{
    static void Main()
    {
        Employee E = new Employee();
        E.GetInfo();
    }
}
/*
Output
Name: John L. Malgraine
SSN: 444-55-6666
Employee ID: ABC567EFG
*/
```

# this

`this` 关键字指代类的当前实例,还可用作扩展方法的第一个参数的修饰符

访问限定类似名称隐藏的成员
```CSharp
public class Employee
{
    private string alias;
    private string name;

    public Employee(string name, string alias)
    {
        // Use this to qualify the members of the class
        // instead of the constructor parameters.
        this.name = name;
        this.alias = alias;
    }
}
```

将本体对象作为参数传递
```CSharp
CalcTax(this);
```

声明索引器
```CSharp
public int this[int param]
{
    get { return array[param]; }
    set { array[param] = value; }
}
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
