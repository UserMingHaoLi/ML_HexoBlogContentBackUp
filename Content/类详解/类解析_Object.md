---
title: 类解析-Object
date: 2021-05-12 21:32:00
updated: 2021-05-12 21:32:00
id: ml-20210512-213200-g139
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

语言通常不需要类来声明继承 `Object` ，因为继承是隐式的

因为 .NET 中的所有类都是从派生的 `Object` ，所以类中定义的每个方法 `Object` 都可用于系统中的所有对象

所以, 任何类都有以下方法

*  public virtual bool Equals(Object? obj);
*  public virtual int GetHashCode();
*  public Type GetType();
*  public virtual string? ToString();

> 注意到了吗,`GetType`是无法重写的,这防止类型隐瞒自身真实的`Type`

然后`Object`还保留一个  
* protected Object MemberwiseClone();

之后是两个静态方法  
* Equals(Object? objA, Object? objB);
* ReferenceEquals(Object? objA, Object? objB);

> 还有构造器`public Object();`,和构析器`~Object();`

<!--more-->

# 性能注意事项

对类型进行装箱和取消装箱的过程会产生性能开销

* 创建接受类型的常规方法 `Object` ，并创建一组特定于类型的方法重载，这些重载接受你希望类经常处理的每个值类型

* 将类型及其成员设计为使用泛型

# Equals

```C#
public virtual bool Equals(Object obj)
{
	return RuntimeHelpers.Equals(this, obj);
}
//RuntimeHelpers.Equals 如下
[System.Security.SecuritySafeCritical]  // auto-generated
[ResourceExposure(ResourceScope.None)]
[MethodImplAttribute(MethodImplOptions.InternalCall)]
public new static extern bool Equals(Object o1, Object o2);
```

使用object的比较可能产生额外性能开销,建议使用
```C#
interface IEquatable<T>
```
*不过并不能确认外部调用了那个`Equals`,所以还是要重写`Object`的`Equals`*

* 如果当前实例是引用类型，则 `Equals(Object)` 方法会测试引用相等性，并调用 `Equals(Object)` 方法等效于调用 `ReferenceEquals` 方法
  * 可以重写引用类型的的默认实现 `Equals` ，以测试值相等性而不是引用相等性，并定义值相等性的精确含义
* 如果当前实例是值类型，则方法会 `Equals(Object)` 测试值是否相等
  * 对于值类型，应始终重写 `Equals` ，因为依赖于反射的相等性测试会**降低性能**
* 枚举	`Enum.Equals`	值必须具有相同的枚举类型和基础值。
* 委托	`MulticastDelegate.Equals`	委托必须具有相同的调用列表类型。
* 接口	`Object.Equals(Object)`	引用相等性。

例如，对象的值 `String` 基于字符串的字符; `String.Equals(Object)` 方法会重写 `Object.Equals(Object)` 方法，以便 `true` 为任意两个包含相同字符的字符串实例返回相同的顺序

重写示例
```C#
public override bool Equals(Object obj)
{
	Person personObj = obj as Person;
	if (personObj == null)
		return false;
	else
		return idNumber.Equals(personObj.idNumber);
}
```

**对于相等性比较,要注意一致,传递,交换,Null**  
* `Equals` 不得引发异常*

重写时，请遵循以下准则 `Equals(Object)` ：

* 实现的类型 `IComparable` 必须重写 `Equals(Object)` 
* 重写的类型 `Equals(Object)` 还必须重写 `GetHashCode` ; 否则，哈希表可能无法正常工作
* 应考虑实现 `IEquatable<T>` 接口，以支持强类型化测试是否相等。 你的 `IEquatable<T>.Equals` 实现应返回与`Equals(Object)`一致的结果 
* 对于`==`和`Equals(Object)`,应该一并重载,并得出一致的结果
  * 大多数引用类型不得重载相等运算符（即使它们重写`Equals`） 。 但是，如果要实现的引用类型应具有值语义，则必须重写相等运算符
* 不应覆盖 `Equals` 可变引用类型,这意味着可变引用类型的实例的哈希代码在其生存期内可能会更改,导致对象在哈希表中丢失

# GetHashCode

作为默认哈希函数
```C#
public virtual int GetHashCode()
{
	return RuntimeHelpers.GetHashCode(this);
}
[System.Security.SecuritySafeCritical]
[ResourceExposure(ResourceScope.None)]
[MethodImplAttribute(MethodImplOptions.InternalCall)]
public static extern int GetHashCode(Object o);
```

生成哈希代码的一种方法是使用操作来合并这些字段 

```C#
public override int GetHashCode()
{
	return x ^ y;
}
```
*但是,这将使(x,y)(y,x)哈希值相等*

可以考虑使用元组

```C#
public override int GetHashCode()
{
	return Tuple.Create(x, y).GetHashCode();
}
```
*该对象反映每个字段的顺序*

还有一种方法,将两个哈希值按一定权重重叠
```C#
public override int GetHashCode()
{
	return ShiftAndWrap(x.GetHashCode(), 2) ^ y.GetHashCode();
}
```

相等的两个对象返回的哈希代码相等。 但是 **相等的哈希代码不意味着对象相等**

`.NET` 不保证方法的默认实现 `GetHashCode`  
对于不同平台,不同版本,默认的哈希函数可能得到不同的结果

* 不要序列化哈希代码值或将其存储在数据库中。
* 不要使用哈希代码作为密钥来检索键控集合中的对象。
* 不要跨应用程序域或进程发送哈希代码。 在某些情况下，可以基于每个进程或每个应用程序域来计算哈希代码
* 如果需要加密型强哈希,参考`System.Security.Cryptography.HashAlgorithm` 或类的类 `System.Security.Cryptography.KeyedHashAlgorithm `
* 不要测试哈希代码是否相等，以确定两个对象是否相等。 (不相等对象可以具有相同的哈希代码。 ) 要测试是否相等，请调用 `ReferenceEquals` 或 `Equals` 方法。

对于此方法的默认实现`RuntimeHelpers.GetHashCode` 换言之，该方法返回的两个对象 `ReferenceEquals true` 具有相同的哈希代码

如果值类型不重写 `GetHashCode` ，则 `ValueType.GetHashCode` 基类的方法使用反射根据类型字段的值来计算哈希代码  
换句话说，其字段具有相等值的值类型具有相同的哈希代码

*所以,如果重写 `GetHashCode` 方法，则还应重写 `Equals` ，反之亦然*

如果用作哈希表中的键的对象不提供有用的实现 `GetHashCode` ，则可以通过 `IEqualityComparer` 向 `Hashtable` 类构造函数的重载之一提供实现来指定哈希代码提供程序


对于每次允许,同一个对象应返回同样的哈希码,如果重新运行应用程序，则可以返回新的哈希代码(只要本次运行中相同即可)

对对象状态的小修改应该会导致对生成的哈希代码进行较大的修改，以获得最佳的哈希表性能

哈希函数的计算成本应较低

`GetHashCode()` 方法不应引发异常

# GetType

```C#
[System.Security.SecuritySafeCritical]
[Pure]
[ResourceExposure(ResourceScope.None)]
[MethodImplAttribute(MethodImplOptions.InternalCall)]
public extern Type GetType();
```

获取当前实例的 Type

返回当前实例的准确运行时类型

因为 `System.Object` 是 `.net` 类型系统中所有类型的基类，所以 `GetType` 方法可用于返回 `Type` 表示所有 `.net` 类型的对象

如果两个对象 `x`,  `y` 具有相同的运行时类型，则 `Object.ReferenceEquals(x.GetType(),y.GetType())` 返回 `true`  
*也就是说,得到的`Type`是相同的*

# ToString

返回表示当前对象的字符串

```C#
public virtual String ToString()
{
	return GetType().ToString();
}
//Type.ToString如下
public override String ToString()
{
	return "Type: " + Name;
}
```
*这意味着,如果不实现自己的`ToString`, 默认实现会返回对象类型的完全限定名称*

基础类型中`结构`和`枚举`都默认重写了`ToString`

许多类型还重载 `ToString` 方法以提供接受多种参数的版本

下面的示例调用重载 `Decimal.ToString(String, IFormatProvider)` 的方法，以显示货币值的区分区域性的格式设置

```C#
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "en-GB", "fr-FR",
                                "hr-HR", "ja-JP" };
      Decimal value = 1603.49m;
      foreach (var cultureName in cultureNames) {
         CultureInfo culture = new CultureInfo(cultureName);
         Console.WriteLine("{0}: {1}", culture.Name,
                           value.ToString("C2", culture));
      }
   }
}
// The example displays the following output:
//       en-US: $1,603.49
//       en-GB: £1,603.49
//       fr-FR: 1 603,49 €
//       hr-HR: 1.603,49 kn
//       ja-JP: ¥1,603.49
```

## 扩展对象 ToString 方法

尽管可能需要 `ToString` 数组或集合类来显示其成员的值，但它将显示类型完全限定的类型名称.  
这不是我们希望的. 所以可以有以下几个方式.

* 对象或实现 `IEnumerable` 或 `IEnumerable<T>` ，则可以使用 `foreach`, 遍历每个单位输出  
* 如果类不 `sealed`, 则可以继承并重写`ToString`方法.
* 使用`扩展方法`

```C#
public class CList<T> : List<T>
{
   public CList(IEnumerable<T> collection) : base(collection)
   { }

   public CList() : base()
   {}

   public override string ToString()
   {
      string retVal = string.Empty;
      foreach (T item in this) {
         if (string.IsNullOrEmpty(retVal))
            retVal += item.ToString();
         else
            retVal += string.Format(", {0}", item);
      }
      return retVal;
   }
}
```

```C#
public static string ToString2<T>(this List<T> l)
{
	string retVal = string.Empty;
	foreach (T item in l)
		retVal += string.Format("{0}{1}", string.IsNullOrEmpty(retVal) ?
													"" : ", ",
								item);
	return string.IsNullOrEmpty(retVal) ? "{}" : "{ " + retVal + " }";
}
```

> 不推荐自己实现`IStringable`

需要更好地控制格式设置的派生类 `ToString()` 可以实现 `IFormattable` 接口

方法的重写 `ToString()` 应遵循以下准则

* 返回的字符串应该是友好的，可供人们阅读。
* 返回的字符串应唯一标识对象实例的值。
* 返回的字符串应尽可能短，以便调试器能够显示它。
* `ToString()` 重写不应返回 `Empty` 或为空字符串。
* `ToString()` 重写不应引发异常。
* 如果实例的字符串表示形式区分区域性或可通过多种方式进行格式化，则实现 `IFormattable` 接口
* 如果返回的字符串包含敏感信息，则应首先请求适当的权限。 如果请求成功，则可以返回敏感信息;否则，应返回排除敏感信息的字符串
* `ToString()` 重写应没有可观察到的副作用，以避免调试中的复杂。 例如，对方法的调用 `ToString()` 不应更改实例字段的值
* 如果你的类型可以通过构造函数或从字符串实例化,则应确保该方法返回的字符串 `ToString()` 可转换为对象实例

# Windows运行时

有些类可能不是继承自`Object`, 但是会由 `.NET Framework` 提供这些方法的默认行为

# MemberwiseClone

```C#
[System.Security.SecuritySafeCritical] 
[ResourceExposure(ResourceScope.None)]
[MethodImplAttribute(MethodImplOptions.InternalCall)]
protected extern Object MemberwiseClone();
```

执行浅表复制操作  

将当前对象的非静态字段复制到新的对象。 如果字段是值类型，则执行字段的逐位副本。 如果字段是引用类型，则会复制引用，但不会复制引用的对象;因此，原始对象及其复本引用相同的对象。

浅层复制不能满足需要,则

* 使用构造函数创建一个新的
* 调用 `MemberwiseClone` 后手动赋值引用类型
* 序列化要深层复制的对象，然后将序列化的数据还原到其他对象变量
* 使用带有递归的反射来执行深层复制操作

# 静态Equals

```C#
public static bool Equals(Object objA, Object objB) 
{
   if (objA==objB) {
      return true;
   }
   if (objA==null || objB==null) {
      return false;
   }
   return objA.Equals(objB);
}
```

可见,先判断引用相同,在判断`Null`,最后是本体的`Equals`.

所以两个`null`可以返回`true`

*如果 `objA` 重写 `Object.Equals(Object)` 方法，则将调用此重写*

# 静态ReferenceEquals

```C#
[ReliabilityContract(Consistency.WillNotCorruptState, Cer.Success)]
[System.Runtime.Versioning.NonVersionable]
public static bool ReferenceEquals (Object objA, Object objB) {
   return objA == objB;
}
```

可见,只比较了`==`

`ReferenceEquals` 在这两种情况下，方法的返回值可能看起来是异常的

* 比较值类型时, 由于装箱,会返回`false`
* 比较字符串时, 取决于该字符串是否是缓存.

```C#
String s1 = "String1";
String s2 = "String1";
Console.WriteLine("s1 = s2: {0}", Object.ReferenceEquals(s1, s2));
Console.WriteLine("{0} interned: {1}", s1,
                  String.IsNullOrEmpty(String.IsInterned(s1)) ? "No" : "Yes");

String suffix = "A";
String s3 = "String" + suffix;
String s4 = "String" + suffix;
Console.WriteLine("s3 = s4: {0}", Object.ReferenceEquals(s3, s4));
Console.WriteLine("{0} interned: {1}", s3,
                  String.IsNullOrEmpty(String.IsInterned(s3)) ? "No" : "Yes");

// The example displays the following output:
//       s1 = s2: True
//       String1 interned: Yes
//       s3 = s4: False
//       StringA interned: No
```
*s3和s4不是缓存,引用不同,则返回`false`*

# 构造

```C#
[ReliabilityContract(Consistency.WillNotCorruptState, Cer.MayFail)]
[System.Runtime.Versioning.NonVersionable]
public Object()
{            
}
```
啥也没做,就创建一个空`Object`

# 析构

```C#
[ReliabilityContract(Consistency.WillNotCorruptState, Cer.Success)]
[System.Runtime.Versioning.NonVersionable]
~Object()
{
}
```
`Finalize`方法用于在销毁对象之前对当前对象占用的非托管资源执行清理操作。 方法是受保护的，因此只能通过此类或派生类访问

`Object`类不提供方法的实现

`Finalize` 操作具有以下限制

* 不确定终结器执行时的准确时间。 若要确保类的实例的资源的确定性版本，请实现 Close 方法或提供 IDisposable.Dispose 实现
* 不能保证两个对象的终结器以任何特定顺序运行
* 不指定终结器在其上运行的线程

在 `Finalize` 以下异常情况下，该方法可能无法运行到完成或根本不会运行

* 如果另一个终结器无限期地阻止 (进入无限循环，则尝试获取它永远无法获取的锁，依此类推) 。 因为运行时尝试运行终结器来完成，所以如果终结器无限期阻塞，则可能不会调用其他终结器
* 如果进程终止，无需给运行时提供清理的机会。 在这种情况下，运行时的进程终止通知是 DLL_PROCESS_DETACH 通知

如果 `Finalize` 或的替代 `Finalize` 引发了异常，并且运行时不是由覆盖默认策略的应用程序承载，则运行时将终止进程，并且不会执行任何活动的 `try / finally` 块或终结器

---

应为 `Finalize` 使用非托管资源的类,因为垃圾回收器会自动释放托管资源

`C #` 编译器不允许重写 `Finalize` 方法。 而是通过实现类的 析构函数 来提供终结器。 `C #` 析构函数自动调用其基类的析构函数

# private

```C#
// Sets the value specified in the variant on the field
// 
[System.Security.SecurityCritical]  // auto-generated
private void FieldSetter(String typeName, String fieldName, Object val)
{
   Contract.Requires(typeName != null);
   Contract.Requires(fieldName != null);

   // Extract the field info object
   FieldInfo fldInfo = GetFieldInfo(typeName, fieldName);

   if (fldInfo.IsInitOnly)
      throw new FieldAccessException(Environment.GetResourceString("FieldAccess_InitOnly"));

   // Make sure that the value is compatible with the type
   // of field
#if FEATURE_REMOTING        
   System.Runtime.Remoting.Messaging.Message.CoerceArg(val, fldInfo.FieldType);    
#else
   Type pt=fldInfo.FieldType;
   if (pt.IsByRef) 
   {
      pt = pt.GetElementType();
   }

   if (!pt.IsInstanceOfType(val))
   {
      val = Convert.ChangeType(val, pt, CultureInfo.InvariantCulture);
   }

#endif

   // Set the value            
   fldInfo.SetValue(this, val);
}

// Gets the value specified in the variant on the field
// 
private void FieldGetter(String typeName, String fieldName, ref Object val)
{
   Contract.Requires(typeName != null);
   Contract.Requires(fieldName != null);

   // Extract the field info object
   FieldInfo fldInfo = GetFieldInfo(typeName, fieldName);

   // Get the value
   val = fldInfo.GetValue(this);            
}

// Gets the field info object given the type name and field name.
// 
private FieldInfo GetFieldInfo(String typeName, String fieldName)
{
   Contract.Requires(typeName != null);
   Contract.Requires(fieldName != null);
   Contract.Ensures(Contract.Result<FieldInfo>() != null);

   Type t = GetType();
   while(null != t)
   {
      if(t.FullName.Equals(typeName))
      {
            break;
      }

      t = t.BaseType;
   }
   
   if (null == t)
   {
#if FEATURE_REMOTING         
      throw new RemotingException(String.Format(
            CultureInfo.CurrentCulture, Environment.GetResourceString("Remoting_BadType"),
                                          typeName));
#else
      throw new ArgumentException();
#endif
   }

   FieldInfo fldInfo = t.GetField(fieldName, BindingFlags.Public | 
                                             BindingFlags.Instance | 
                                             BindingFlags.IgnoreCase);
   if(null == fldInfo)
   {
#if FEATURE_REMOTING 
      throw new RemotingException(String.Format(
            CultureInfo.CurrentCulture, Environment.GetResourceString("Remoting_BadField"),
                                          fieldName, typeName));            
#else
      throw new ArgumentException();
#endif

   }
   
   return fldInfo;
}
```

搜了一圈没找到是干啥用的,推测可能是反射或者泛型使用.

## 内部方法 __Canon

用于实例化`规范化`的方法列表,来具现泛型实例

用户不会看到他们,但堆栈或者反编译工具经常能见到他.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
