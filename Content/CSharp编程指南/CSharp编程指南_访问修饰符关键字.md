---
title: CSharp编程指南_访问修饰符关键字
date: 2021-04-17 21:28:00
updated: 2021-04-17 21:28:00
id: ml-20210417-212800-g122
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

# 访问修饰符

访问修饰符用于指定成员或类型已声明的可访问性.

# 快速参考

关键字  
* `public`
* `protected`
* `internal`
* `private`

有以下声明表示  
* `public`：访问不受限制。
* `protected`：访问限于包含类或派生自包含类的类型。
* `internal`：访问限于当前程序集。
* `protected internal`：访问限于当前程序集或派生自包含类的类型。
* `private`：访问限于包含类。
* `private protected`：访问限于包含类或当前程序集中派生自包含类的类型。

<!--more-->

除使用 `protected internal` 或`private protected` 组合的情况外,一个成员或类型仅允许一个访问修饰符

命名空间中不允许出现访问修饰符。 命名空间没有任何访问限制(也就是默认`public`)

如果不指定,所有成员都有默认的访问修饰符  
在`CSharp编程指南_基本概念`一文的`声明的可访问性`章节中有基本介绍,  
本次详细说明如下

嵌套类型的可访问域不能超出包含类型的可访问域

关于`可访问性级别`,`可访问性域`,`可访问性级别的使用限制`  
这些都是由编译器完成的, 如果你的声明不符合其规定,会有提示,这里就不细讲了.

# public

公共访问是允许的最高访问级别。 对访问公共成员没有限制

```CSharp
class PointTest
{
    public int x;
    public int y;
}

class Program
{
    static void Main()
    {
        var p = new PointTest();
        p.x = 10;//完全公开,可访问
        p.y = 15;
        Console.WriteLine($"x = {p.x}, y = {p.y}");
    }
}
// Output: x = 10, y = 15
```
*就是完全公开,允许任意访问*

# protected

受保护成员在其所在的类中可由派生类实例访问  
也就是说除了自己类可以访问外,派生类也可以访问,其他类则不能访问

```CSharp
class A
{
    protected int x = 123;
}

class B : A
{
    static void Main()
    {
        var a = new A();
        var b = new B();

        // a.x = 10;//外部类不可直接访问
        b.x = 10;//继承类可以访问
    }
}
```
*对于自己和继承者来说,可以任意访问,其他则不能访问*

# internal

只有在同一程序集的文件中,内部类型或成员才可访问  
也就是说,外部程序集无法访问

内部访问通常用于基于组件的开发,因为它可使一组组件以私有方式进行协作,而不必向应用程序代码的其余部分公开

*以下实例演示了其作用*
```CSharp
//Assembly1
internal class BaseClass1
{  
   public static int intM = 0;  
}
// Assembly1
public class BaseClass2
{  
   internal static int intM = 0;  
}
// Assembly2
//不处于同一程序集
public class TestAccess
{  
   static void Main()
   {  
		//var myBase1 = new BaseClass1();   // 外部程序集不可访问`internal`
      	var myBase2 = new BaseClass2();   //可以声明`public` 
      	//BaseClass2.intM = 444;    // 外部程序集不可访问`internal`
   }  
}
```
*对于同一程序集,可以任意访问,其他则不能访问*


# private

私有访问是允许的最低访问级别。 私有成员只有在声明它们的类和结构体中才是可访问的

同一体中的嵌套类型也可以访问那些私有成员

在声明私有成员的类或结构外引用它会导致编译时错误

*以下实例演示了其作用*
```CSharp
//外界只能访问 GetName() 和 Salary
class Employee2
{
    private string name = "FirstName, LastName";
    private double salary = 100.0;

    public string GetName()
    {
        return name;
    }

    public double Salary
    {
        get { return salary; }
    }
}

class PrivateTest
{
    static void Main()
    {
        var e = new Employee2();
        //    string n = e.name;//外部类不可访问`private`
        //    double s = e.salary;
        string n = e.GetName();//访问`public`
        double s = e.Salary;
    }
}
```
*对于同一类中,可以任意访问,其他则不能访问*

# protected internal

可从当前程序集或派生自包含类的类型访问受保护的内部成员

结构成员不能为 `protected internal`,因为无法继承结构

```CSharp
// Assembly1
public class BaseClass
{
   protected internal int myValue = 0;
}

class TestAccess
{
    void Access()
    {
        var baseObject = new BaseClass();
        baseObject.myValue = 5;//程序集内部可以访问
    }
}
// Assembly2
class DerivedClass : BaseClass
{
    static void Main()
    {
        var baseObject = new BaseClass();
        var derivedObject = new DerivedClass();

        // baseObject.myValue = 10;//程序集外部不能访问
        derivedObject.myValue = 10;//继承者可以访问
    }
}
```
*可以看到,基本就是`protected`,`internal`的公开部分的并集*  
*对于程序集内部和继承者,可以任意访问,其他则不能访问*

# private protected

仅派生自包含类的类型可访问私有受保护成员,但仅能在其包含程序集中访问  
`private protected` 访问修饰符在 C# 版本 7.2 及更高版本中有效

```CSharp
// Assembly1
public class BaseClass
{
    private protected int myValue = 0;
}

public class DerivedClass1 : BaseClass
{
    void Access()
    {
        var baseObject = new BaseClass();

        // baseObject.myValue = 5;//内部程序集非继承也不可以访问
        myValue = 5;//继承可以访问
    }
}
// Assembly2
class DerivedClass2 : BaseClass
{
    void Access()
    {
        // myValue = 10;//外部程序集继承也不可以访问
    }
}
```
*对于程序集内部的继承者,可以任意访问,其他则不能访问*

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
