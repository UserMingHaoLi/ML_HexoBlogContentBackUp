---
title: UML类图绘制
date: 2019-06-15 18:37:00
updated: 2019-06-15 18:37:00
categories:
	- markdown
	- UML
tags: 
	- markdown
	- UML
---

[toc]
# UML类图

## 地址

本次只讲类图
有道云笔记地址 http://note.youdao.com/noteshare?id=d5f8c56d206389f849b9738adffc3926&sub=73EADB667CA7499E8C7A55834FF75F01  

有机会一定自己部署一个这个类图服务器, 现在图都裂了.

在线绘图地址 http://www.plantuml.com/plantuml/uml/  
文档 http://plantuml.com/zh/class-diagram  
关系参考 https://blog.csdn.net/tianhai110/article/details/6339565  
看下类图 https://blog.csdn.net/qq_35642036/article/details/79663378
<!--more-->
## 语法

在`@startuml`和`enduml`之间编写uml语句

### 标题

关键字`Title`

```
Title "类图标题"
```

### 注释

使用`'`来表示注释  
或者使用`/`来表示块注释

### 创建类, 接口, 抽象类

正常的创建语法即可

```
public class MyClass{
    
}
interface Iinterface{
    
}
abstract class MyAbclass{
    
}
```

### 方法和属性

无他 正常声明即可

```
public static void Main()
```

### 访问权限

四种
* -private
* #protected
* +public
* ~package private //不常用

```
- private void Main()
+ public int GetResult()
# protected void Action()
- private int ccc {get;set;}
```

加上这些符号, 会有一个小icon在方法或者属性的前面

实际的UML原理是, 检测符号, 形成icon, 然后原样copy后面的文字.  
也就是说, 你可以随意的书写内容  
当然, 由于有icon标识了, 我们就不写访问修饰符了.

## 关系

这是一个大块, 所以我提了一级标题.

### 继承

就是正常的类之间继承.  
使用`<|--`来表示一个空心实线箭头. 这就是继承

```
c1 <|-- c2
```

### 实现

实现自纯抽象类或者接口可以使用此表示  
`<|..`这是一个空心虚线箭头  
> 实际上所有的`--`都可以替换为`..`来将实线替换为虚线.

```
i1 <|.. c1
```

### 组合

一个类一定需要另一个类, 否则就都会失效, 这就是组合.  
`*--`  
可以表示为生命周期相同, 机同时生成, 同时销毁.  
一般表示为一个类在另一个类的构造方法中实例化.

### 聚合

一个类包含另一个类, 但是另一个类可以独立存在.  
`o--`  
一般表示为一个类持有一组另一个类

### 关联
强依赖关系, 通常表现为一个类出现在另一个类的属性中  
`-->` 实心实线箭头  
表示一个类持有看另一个类

### 依赖
弱依赖关系哦, 表现为参数关系  
`..>`  
表示一个类需要另一个类做参数, 临时持有

### 域

也可以叫命名空间  
`namespace`
```
class BaseClass

namespace net.dummy #DDDDDD {
    .BaseClass <|-- Person
    Meeting o-- Person

    .BaseClass <|- Meeting
}

namespace net.foo {
  net.dummy.Person  <|- Person
  .BaseClass <|-- Person

  net.dummy.Meeting o-- Person
}

BaseClass <|-- net.unused.Person

class net.unused.Person {
  + public void test();
}
```


## 实践

然后是一个具体的实例

```
@startuml
class 氧气
class 水
class 动物{
+ 有生命
+ 新陈代谢(氧气 o2, 水 h2o)
+ 繁衍()
}
class 鸟{
+ 羽毛
+ 喙
+ 下蛋()
}
class 翅膀
class 鸭子{
+ 下蛋()
}
class 企鹅{
+ 下蛋()
}
class 大雁{
+飞行()
+下蛋()
}
class 气候
class 燕群{
+ V字飞行()
+ 一字飞行()
}
interface 飞行{
+ 飞()
}
interface 人类说话{
+讲话()
}
class 唐老鸭{
+讲话()
}

氧气 <.. 动物
水 <.. 动物
动物 <|-- 鸟
翅膀 <--* 鸟
鸟 <|-- 鸭子
鸟 <|-- 企鹅
鸟 <|-- 大雁
鸭子 <|-- 唐老鸭
人类说话 <|.. 唐老鸭
气候 <-- 企鹅
大雁 <--o 燕群
飞行 <|.. 大雁
@enduml
```


他最后是这样的

![UMLClassMap][image1]

[image1]: http://www.plantuml.com/plantuml/png/RLBDJi904BxlK-mHnFR0kGUUmZj1Z2O82U09JTe3Ag899eWXg0GXGG_OQdhWH_1bkjtjMpZhRDfLEK3d-_acqo_DrUhvQltak4GAfNojHaDl6dhT6FXgObSllFNQ81aQFhHuTyXkrtBkUL7_CbYDXVUSmYIjP7TbZXvcAscmy8KZHZUfD3bJcwBvi27vpJfq1p2mNX_-WtbR3DgQZNyrnRaTPsPircbiymMVJhHeRfNOP1eD792YyPCONOEI-UU1YvdTLu1VtF7DHExQOxFx90MhROs0y56vNgmUv0j5YYHavFH72vPB_huIhY_SHt28jmyOz0Ex7M4xy7A_HQAQj4nJzGwChqDyKElKC6IV1CkYbc7i_63ueOZ59HZRIZ1MHD26L7mKqSy7pJGr3NkZLh8JLqcceeeas0r6KIQvOdaVFhPl


元地址如下
```
![UMLClassMap][image1]

[image1]: http://www.plantuml.com/plantuml/png/RLBDJi904BxlK-mHnFR0kGUUmZj1Z2O82U09JTe3Ag899eWXg0GXGG_OQdhWH_1bkjtjMpZhRDfLEK3d-_acqo_DrUhvQltak4GAfNojHaDl6dhT6FXgObSllFNQ81aQFhHuTyXkrtBkUL7_CbYDXVUSmYIjP7TbZXvcAscmy8KZHZUfD3bJcwBvi27vpJfq1p2mNX_-WtbR3DgQZNyrnRaTPsPircbiymMVJhHeRfNOP1eD792YyPCONOEI-UU1YvdTLu1VtF7DHExQOxFx90MhROs0y56vNgmUv0j5YYHavFH72vPB_huIhY_SHt28jmyOz0Ex7M4xy7A_HQAQj4nJzGwChqDyKElKC6IV1CkYbc7i_63ueOZ59HZRIZ1MHD26L7mKqSy7pJGr3NkZLh8JLqcceeeas0r6KIQvOdaVFhPl
```
