---
title: CSharp编程指南_查询关键字
date: 2021-04-26 23:42:00
updated: 2021-04-26 23:42:00
id: ml-20210426-234200-g133
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

也就是大名鼎鼎的`LINQ`强化写法.  
类似于SQL语句.

| 子句       | 说明                                                                            |
| ---------- | ------------------------------------------------------------------------------- |
| from       | 指定数据源和范围变量（类似于迭代变量）。                                        |
| where      | 基于由逻辑 AND 和 OR 运算符（&& 或 `||`）分隔的一个或多个布尔表达式筛选源元素。 |
| select     | 指定执行查询时,所返回序列中元素的类型和形状。                                  |
| group      | 根据指定的密钥值对查询结果分组。                                                |
| into       | 提供可作为对 join、group 或 select 子句结果引用的标识符。                       |
| orderby    | 根据元素类型的默认比较器对查询结果进行升序或降序排序。                          |
| join       | 基于两个指定匹配条件间的相等比较而联接两个数据源。                              |
| let        | 引入范围变量,在查询表达式中存储子表达式结果。                                  |
| in         | join 子句中的上下文关键字。                                                     |
| on         | join 子句中的上下文关键字。                                                     |
| equals     | join 子句中的上下文关键字。                                                     |
| by         | group 子句中的上下文关键字。                                                    |
| ascending  | orderby 子句中的上下文关键字。                                                  |
| descending | orderby 子句中的上下文关键字。                                                  |

<!--more-->

# form

查询表达式必须以 `from` 子句开头。 此外,查询表达式可包含也以 `from` 子句开头的子查询

`from` 子句指定下列各项：  
* 将在其上运行查询或子查询的数据源。
* 表示源序列中每个元素的本地范围变量。

`from` 子句中引用的数据源必须具有 `IEnumerable`、`IEnumerable<T>` 类型之一,或 `IQueryable<T>` 等派生类型

```CSharp
var lowNums = from num in numbers
            where num < 5
            select num;
```
*此例子查询所有`numbers`中小于5的数字,并返回为`lowNums`*

```CSharp
var scoreQuery = from student in students
                from score in student.Scores
                where score > 90
                select new { Last = student.LastName, score };
```
*此例子演示了嵌套`from`*

```CSharp
var joinQuery2 =
    from lower in lowerCase
    where lower != 'x'
    from upper in upperCase
    select new { lower, upper };
```

# where

`where` 子句用在查询表达式中,用于指定将在查询表达式中返回数据源中的哪些元素

上面`form`就有例子.

# select

`select` 子句指定在执行查询时产生的值的类型

```CSharp
 IEnumerable<int> queryHighScores =
            from score in Scores
            where score > 80
            select score;
```
*推断出`score`是`int`类型, 所以最终返回`IEnumerable<int>`*

# group

`group` 子句返回一个 `IGrouping<TKey,TElement>` 对象序列  
这些对象包含零个或更多与该组的键值匹配的项

```CSharp
IEnumerable<IGrouping<char, Student>> studentQuery1 =
    from student in students
    group student by student.Last[0];

```

如果要对每个组执行附加查询操作,可使用上下文关键字 `into` 指定一个临时标识符

```CSharp
IEnumerable<IGrouping<char, Student>> studentQuery2 =
    from student in students
    group student by student.Last[0] into g
    orderby g.Key
    select g;
```

# into

`into` 上下文关键字创建临时标识符,将 `group`、`join` 或 `select` 子句的结果存储至新标识符

例子同上.

# orderby 

`orderby` 子句可导致返回的序列或子序列（组）以升序或降序排序

```CSharp
IEnumerable<string> sortAscendingQuery =
    from fruit in fruits
    orderby fruit
    select fruit;

 IEnumerable<string> sortDescendingQuery =
    from w in fruits
    orderby w descending
    select w;
```

第一个查询按字母顺序从 A 开始对字词排序,而第二个查询则按降序对相同的字词排序

```CSharp
IEnumerable<Student> sortedStudents =
    from student in students
    orderby student.Last ascending, student.First ascending
    select student;
```
*可连续使用*  
对学生的姓氏进行主要排序,然后对其名字进行次要排序

> 编译时,`orderby` 子句将转换为对 `OrderBy` 方法的调用。 `orderby` 子句中的多个关键值将转换为 `ThenBy` 方法调用。

# join

`join` 子句可用于将来自不同源序列并且在对象模型中没有直接关系的元素相关联  
要求是每个源中的元素需要共享某个可以进行比较以判断是否相等的值

```CSharp
//定义数据
class Product
{
    public string Name { get; set; }
    public int CategoryID { get; set; }
}

class Category
{
    public string Name { get; set; }
    public int ID { get; set; }
}

// Specify the first data source.
List<Category> categories = new List<Category>()
{
    new Category {Name="Beverages", ID=001},
    new Category {Name="Condiments", ID=002},
    new Category {Name="Vegetables", ID=003},
    new Category {Name="Grains", ID=004},
    new Category {Name="Fruit", ID=005}
};

// Specify the second data source.
List<Product> products = new List<Product>()
{
    new Product {Name="Cola",  CategoryID=001},
    new Product {Name="Tea",  CategoryID=001},
    new Product {Name="Mustard", CategoryID=002},
    new Product {Name="Pickles", CategoryID=002},
    new Product {Name="Carrots", CategoryID=003},
    new Product {Name="Bok Choy", CategoryID=003},
    new Product {Name="Peaches", CategoryID=005},
    new Product {Name="Melons", CategoryID=005},
};
```

## 内部联接

```CSharp
var innerJoinQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID
    select new { ProductName = prod.Name, Category = category.Name }; 
```
如果 `category.ID equals prod.CategoryID` 不成立,则该`products`不会出现在最终列表中

```CSharp
/*
Cola      1
Tea       1
Mustard   2
Pickles   2
Carrots   3
Bok Choy  3
Peaches   5
Melons    5
InnerJoin: 8 items in 1 group.
*/
```

## 分组链接

含有 `into` 表达式的 `join` 子句称为分组联接

分组联接会生成分层的结果序列,该序列将左侧源序列中的元素与右侧源序列中的一个或多个匹配元素相关联。 分组联接没有等效的关系术语；它本质上是一个对象数组序列

如果在右侧源序列中找不到与左侧源中的元素相匹配的元素,则 `join` 子句会为该项生成一个空数组。 因此,分组联接基本上仍然是一种内部同等联接,区别在于分组联接将结果序列组织为多个组

```CSharp
var groupJoinQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    select prodGroup;

foreach (var prodGrouping in groupJoinQuery)
{
    Console.WriteLine("Group:");
    foreach (var item in prodGrouping)
    {
        totalItems++;
        Console.WriteLine("   {0,-10}{1}", item.Name, item.CategoryID);
    }
}
/*
Group:
    Cola      1
    Tea       1
Group:
    Mustard   2
    Pickles   2
Group:
    Carrots   3
    Bok Choy  3
Group:
Group:
    Peaches   5
    Melons    5
Unshaped GroupJoin: 8 items in 5 unnamed groups
*/
```
*因为`join`的目标是`prod`所以,实际上`into`结果`prodGroup`里面的小项也都是`prod`*

```CSharp
var groupJoinQuery2 =
    from category in categories
    orderby category.ID
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    select new
    {
        Category = category.Name,
        Products = from prod2 in prodGroup
                    orderby prod2.Name
                    select prod2
    };
```
```CSharp
foreach (var productGroup in groupJoinQuery2)
{
    Console.WriteLine(productGroup.Category);
    foreach (var prodItem in productGroup.Products)
    {
        totalItems++;
        Console.WriteLine("  {0,-10} {1}", prodItem.Name, prodItem.CategoryID);
    }
}
```
```CSharp
/*
Beverages
    Cola       1
    Tea        1
Condiments
    Mustard    2
    Pickles    2
Vegetables
    Bok Choy   3
    Carrots    3
Grains
Fruit
    Melons     5
    Peaches    5
*/
```
*比上个例子多出一个`Select`集合,以及`products`以`Name`排序*

```CSharp
var groupJoinQuery3 =
    from category in categories
    join product in products on category.ID equals product.CategoryID into prodGroup
    from prod in prodGroup
    orderby prod.CategoryID
    select new { Category = prod.CategoryID, ProductName = prod.Name };
Console.WriteLine("GroupJoin3:");
foreach (var item in groupJoinQuery3)
{
    totalItems++;
    Console.WriteLine("   {0}:{1}", item.ProductName, item.Category);
}
/*
GroupJoin3:
    Cola:1
    Tea:1
    Mustard:2
    Pickles:2
    Carrots:3
    Bok Choy:3
    Peaches:5
    Melons:5
*/
```
*指定了一个新集合,按ID排序*

## 左外部链接

在左外部联接中,将返回左侧源序列中的所有元素,即使右侧序列中没有其匹配元素也是如此。 若要在 LINQ 中执行左外部联接,请结合使用 `DefaultIfEmpty` 方法与分组联接

```CSharp
var leftOuterQuery =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    select prodGroup.DefaultIfEmpty(new Product() { Name = "Nothing!", CategoryID = category.ID });
foreach (var prodGrouping in leftOuterQuery)
{
    Console.WriteLine("Group:");
    foreach (var item in prodGrouping)
    {
        totalItems++;
        Console.WriteLine("  {0,-10}{1}", item.Name, item.CategoryID);
    }
}
/*
Left Outer Join:
Group:
    Cola      1
    Tea       1
Group:
    Mustard   2
    Pickles   2
Group:
    Carrots   3
    Bok Choy  3
Group:
    Nothing!  4
Group:
    Peaches   5
    Melons    5
*/
```
*这里的左侧是`category.ID`, 所以生成所有`ID`. 1,2,3,4,5. 但是没有ID为4的商品, 所以需要指定`DefaultIfEmpty(new Product() `为无匹配时的商品*

```CSharp
var leftOuterQuery2 =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    from item in prodGroup.DefaultIfEmpty()
    select new { Name = item == null ? "Nothing!" : item.Name, CategoryID = category.ID };
foreach (var item in leftOuterQuery2)
{
    totalItems++;
    Console.WriteLine("{0,-10}{1}", item.Name, item.CategoryID);
}
/*
Left Outer Join 2:
Cola      1
Tea       1
Mustard   2
Pickles   2
Carrots   3
Bok Choy  3
Nothing!  4
Peaches   5
Melons    5
*/
```
*这里最后的`Select`抹平了`Group`,所以就是一整组*

# let

在查询表达式中,存储子表达式的结果

```CSharp
var earlyBirdQuery =
    from sentence in strings
    let words = sentence.Split(' ')
    from word in words
    let w = word.ToLower()
    where w[0] == 'a' || w[0] == 'e'
        || w[0] == 'i' || w[0] == 'o'
        || w[0] == 'u'
    select word;
```
*就是查询表达式中的变量类型*

# in

在以下上下文中,使用了 in 关键字：

* 泛型接口和委托中的泛型类型参数。
* 作为参数修饰符,它允许按引用而不是按值向方法传递参数。
* `foreach` 语句。
* **`LINQ` 查询表达式中的 `from` 子句。**
* **`LINQ` 查询表达式中的 `join` 子句。**

# on

`on` 上下文关键字用于在查询表达式的 `join` 子句中指定联接条件

```CSharp
join prod in products on category.ID equals prod.CategoryID
```
# equals

`equals` 上下文关键字用于在查询表达式的 `join` 子句中比较两个序列的元素

同上

# by

`by` 上下文关键字用于在查询表达式的 `group` 子句中指定应返回项的分组方式

```CSharp
var query = from student in students
    group student by student.LastName[0];
```

# ascending

查询表达式中的 `orderby` 子句 中使用 `ascending` 上下文关键字指定排序顺序为从小到大。 由于 `ascending` 为默认排序顺序,因此无需加以指定。

```CSharp
IEnumerable<string> sortAscendingQuery =
    from vegetable in vegetables
    orderby vegetable ascending
    select vegetable;
```

# descending

查询表达式中的 `orderby` 子句中使用 `descending` 上下文关键字指定排序顺序为从大到小。

```CSharp
IEnumerable<string> sortDescendingQuery =
    from vegetable in vegetables
    orderby vegetable descending
    select vegetable;
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
