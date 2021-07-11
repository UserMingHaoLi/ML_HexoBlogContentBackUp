---
title: CSharp类解析-接口
date: 2021-05-18 20:50:00
updated: 2021-05-18 20:50:00
id: ml-20210518-205000-g141
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---


- [System中的接口](#system中的接口)
  - [IAsyncDisposable](#iasyncdisposable)
  - [IAsyncResult](#iasyncresult)
    - [AsyncState](#asyncstate)
    - [AsyncWaitHandle](#asyncwaithandle)
    - [CompletedSynchronously](#completedsynchronously)
    - [IsCompleted](#iscompleted)
  - [ICloneable](#icloneable)
  - [IComparable](#icomparable)
  - [IComparable<T>](#icomparablet)
  - [IConvertible](#iconvertible)
  - [ICustomFormatter](#icustomformatter)
  - [IDisposable](#idisposable)
  - [IEquatable<T>](#iequatablet)
  - [IFormatProvider](#iformatprovider)
  - [IFormattable](#iformattable)
  - [IObservable<T>](#iobservablet)
  - [IObserver<T>](#iobservert)
  - [IProgress<T>](#iprogresst)
  - [IServiceProvider](#iserviceprovider)
- [System.Collections中的接口](#systemcollections中的接口)
  - [ICollection](#icollection)
  - [IComparer](#icomparer)
  - [IDictionary](#idictionary)
  - [IDictionaryEnumerator](#idictionaryenumerator)
  - [IEnumerable](#ienumerable)
  - [IEnumerator](#ienumerator)
  - [IEqualityComparer](#iequalitycomparer)
  - [IHashCodeProvider](#ihashcodeprovider)
  - [IList](#ilist)
  - [IStructuralComparable](#istructuralcomparable)
- [System.Collections.Generic中的接口](#systemcollectionsgeneric中的接口)
- [完毕](#完毕)


<!--more-->

# System中的接口


## IAsyncDisposable

是老朋友`Disposable`的异步版本  
表示异步释放

* `DisposeAsync()`	
  * 以异步方式执行与释放或重置非托管资源相关的应用程序定义的任务。

使用此方法（而不是 `IDisposable.Dispose` ），可以在不阻止 `GUI` 应用程序的主线程的情况下执行占用大量资源的释放操作

> 异步方法与 `async` `await` 配合使用更佳

您可以在以下情况下实现 `IAsyncDisposable`

* 开发拥有非托管资源的异步枚举器时
* 释放资源需要消耗大量性能

扩展方法 `ConfigureAwait(IAsyncDisposable, Boolean)	`  
配置如何执行从异步可处置项返回的任务的等待

## IAsyncResult

表示异步操作的状态

* `AsyncState`	
  * 获取一个用户定义的对象，该对象限定或包含有关异步操作的信息。
* `AsyncWaitHandle`	
  * 获取用于等待异步操作完成的 WaitHandle。
* `CompletedSynchronously`	
  * 获取一个值，该值指示异步操作是否同步完成。
* `IsCompleted`	
  * 获取一个值，该值指示异步操作是否已完成。

### AsyncState

```C#
public object? AsyncState { get; }
```
可以看到是一个`Object`, 用户定义的任何类型都可以放在里面

### AsyncWaitHandle

```C#
public System.Threading.WaitHandle AsyncWaitHandle { get; }
```

`System.Threading.WaitHandle`有点复杂, 一般使用

* `WaitOne()`
  * 阻止当前线程，直到当前 `WaitHandle` 收到信号

### CompletedSynchronously

```C#
public bool CompletedSynchronously { get; }
```

如果在委托中检测到调用的同步完成 `AsyncCallback` ，则启动异步操作的线程可能是当前线程

例如 如果 `i/o` 请求太小, 可以直接在主线程完成

### IsCompleted

```C#
public bool IsCompleted { get; }
```

可以使用属性来轮询完成

```C#
 while(result.IsCompleted == false) {
                Thread.Sleep(250);
                Console.Write(".");
            }
```

## ICloneable

支持克隆，即用与现有实例相同的值创建类的新实例

* `Clone()`	
  * 创建作为当前实例副本的新对象

该方法旨在提供超出`Object.MemberwiseClone`的克隆支持. 即深拷贝.

建议 `ICloneable` 不要在公共 api 中实现,因为使用者并不理解此次拷贝的深度如何.

## IComparable

定义由值类型或类实现的特定于类型的通用比较方法，旨在对其实例进行排序

* `CompareTo(Object)`	
  * 将当前实例与同一类型的另一个对象进行比较，并返回一个整数，该整数指示当前实例在排序顺序中的位置是位
  于另一个对象之前、之后还是与其位置相同。

很多提供排序的方法都会要求被排序单位实现此接口,如`Array.Sort` `ArrayList.Sort`

其返回值含义如下

* 小于零	
  * 当前实例 `CompareTo` 在排序顺序中位于方法所指定的对象之前。
* 零	
  * 此当前实例与方法所指定的对象在排序顺序中出现的位置相同 `CompareTo`
* 大于零	
  * 此当前实例 `CompareTo` 在排序顺序中跟随方法所指定的对象。

> 由于参数为`Object`,需要注意装箱拆箱, 推荐使用泛型模式,性能好点.  
> 对于比较性质的方法,注意**相等性**的各种条目(对称,交换,传递,重复可靠).  
> 既然有相等性.最好也实现相等性比较相关内容

## IComparable<T>

上面`IComparable`的泛型版本,减少性能消耗,装箱拆箱问题.

* `CompareTo(T)`
  * 同上

通常由` List<T>.Sort()`这种排序方法调用,要求`Item`实现此接口

## IConvertible

**此 API 不符合 CLS**

定义将实现引用或值类型的值转换为具有等效值的公共语言运行时类型的方法

* `GetTypeCode()`	
  * 返回此实例的 TypeCode。
* `ToXXXX(IFormatProvider)`	
  * 使用指定的区域性特定格式设置信息将此实例的值转换为等效的 `XXX` 值, 此种函数有多个,如`ToInt16`,`ToInt32`等待...

我们自己一般不会继承此接口.通常直接使用`Convert`类

大多数转换方法具有类型为的参数 `IFormatProvider` ，该参数表示当前区域性 (`CurrentCulture`) 或特定区域性。 大多数情况下， `IConvertible` 基类型的实现会忽略此参数。 但是，你自己实现的话可以选择是否在代码中使用它

## ICustomFormatter

定义一个方法，该方法支持自定义对象值的格式

* Format(String, Object, IFormatProvider)	
  * 使用指定的格式和区域性特定格式信息将指定对象的值转换为它的等效字符串表示形式
  * `String` 包含格式规范的格式字符串
  * `Object` 要设置其格式的对象
  * `IFormatProvider` 一个对象，提供有关当前实例的区域性特定格式信息
    * 此接口提供`GetFormat(Type)`方法

`ICustomFormatter.Format` 为回调方法。 它由支持自定义格式设置的方法（如或） `String.Format(IFormatProvider, String, Object[])`  `StringBuilder.AppendFormat(IFormatProvider, String, Object[])` 

为复合格式字符串中的每个格式项调用一次实现。 例如，在下面的语句中， `ICustomFormatter.Format` 方法被调用三次

```C#
Console.WriteLine(String.Format(new BinaryFormatter(),"{0} (binary: {0:B}) (hex: {0:H})", byteValue));
```
*因为有三个格式`{}`*

如果提供对象值的自定义表示形式，则需要执行以下操作

* 定义一个实现 `ICustomFormatter` 接口的类
* 定义一个实现 `IFormatProvider` 接口的类, `GetFormat`方法返回实现的实例 `ICustomFormatter`, 通常，单个类实现 `ICustomFormatter` 和 `IFormatProvider`, 在这种情况下，类的 `GetFormat` 实现只返回自身的实例
* 将 `IFormatProvider` 实现作为方法的参数或类似的方法传递给此类函数`String.Format(IFormatProvider, String, Object[])`

由于接受`Object`参数,可能需要做类型判断,并需要注意装箱拆箱  
对于字符串,记得处理`null`值  
对于不支持的内容,考虑` FormatException`异常  
不建议引发其他种类的异常

它们不能与类型的现有格式字符串冲突。 例如，如果要扩展类型的格式 `Int32` ，则不应实现 *"C"、"D"、"E"、"F" 和 "G"* 格式说明符

## IDisposable

提供一种用于释放非托管资源的机制

* `Dispose()`  
* 执行与释放或重置非托管资源关联的应用程序定义的任务。

实现此方法时，请确保通过包含层次结构传播调用来释放所有保存的资源(即,逐级释放)  
总的来说,就是先死亡的先销毁

如果多次调用对象的 `Dispose` 方法，则对象必须忽略第一个调用之后的所有调用  
因为要避免引发除`ObjectDisposedException`之外的异常

用户可能希望资源类型使用特定约定来表示已分配状态与已释放状态。 这种情况的一个示例是流类，通常将其视为 "打开" 或 "已关闭"  
所以有`Close`方法来调用`Dispose`

> 官方推荐使用`using`来自动调用接口释放资源  
> 另一种推荐是在`finally`块中手动调用  
> `using`语句实际上就是`try / finally`的语法糖

**警告: 将 `IDisposable` 接口添加到现有类的一项重大更改。 由于您的类型的预先存在的使用者无法调用 `Dispose` ，因此您不能确定您的类型持有的非托管资源是否会被释放**

额外的释放机制,可以考虑` System.Runtime.InteropServices.SafeHandle `, `构析器`

对于基类,官方推荐了一种写法  
```C#
using Microsoft.Win32.SafeHandles;
using System;
using System.Runtime.InteropServices;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   // Instantiate a SafeHandle instance.
   SafeHandle handle = new SafeFileHandle(IntPtr.Zero, true);

   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   {
      Dispose(true);
      GC.SuppressFinalize(this);
   }

   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return;

      if (disposing) {
         handle.Dispose();
         // Free any other managed objects here.
         //
      }

      disposed = true;
   }
}
```
*可以看到,子类重写的实际不是接口内容,但是接口调用这个继承函数,来达到子类释放的目的*

如果你实现`析构器`, 则应做如下修改
```C#
using System;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;

   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   {
      Dispose(true);
      GC.SuppressFinalize(this);
   }

   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return;

      if (disposing) {
         // Free any other managed objects here.
         //
      }

      // Free any unmanaged objects here.
      //
      disposed = true;
   }

   ~BaseClass()
   {
      Dispose(false);
   }
}
```
*可以看到,构析器调用了`Dispose(false)`*

子类应实现以下可释放模式：  
* 它们必须重写 `Dispose(Boolean)` 并调用基类 `Dispose(Boolean)` 实现。
* 如果需要，他们可以提供终结器。 终结器必须调用 `Dispose(false)`

对于子类,代码如下
```C#
protected override void Dispose(bool disposing)
{
  if (disposed)
      return;

  if (disposing) {
      handle.Dispose();
      // Free any other managed objects here.
      //
  }

  // Free any unmanaged objects here.
  //

  disposed = true;
  // Call base class implementation.
  base.Dispose(disposing);
}
```
*可以看到,这次是先销毁子类,再销毁父类*

## IEquatable<T>

泛型,常用于和补充Object的`Equals`

* `Equals(T)`	
  * 指示当前对象是否等于同一类型的另一个对象。

泛型在面对值类型时提供更好的性能  
若要处理某个类的对象将存储在数组或泛型集合对象中的可能性，最好实现此方法

任何比较需要注意*(对称,交换,传递,重复可靠)*

实现此方法通常还需重写`Equals`,`GetHashCode`,`==`,`!=`  
确保所有相等测试都返回一致的结果

如果你的类型实现 `IComparable<T>` ，你几乎始终都实现 `IEquatable<T>`

## IFormatProvider

提供一种机制，用于检索对象以控制格式化

* `GetFormat(Type)`	
  * 返回一个对象，该对象为指定类型提供格式设置服务

如果 `IFormatProvider` 实现能够提供该类型的对象，则为返回所指定对象的实例；否则为 `null`

就是外界想你要一个类型,你是否能提供,通常,这个类型会用做提供格式化内容,所以本接口实际上是格式化筛选器.  用于通过参数(外界需求)来选取需要的格式化工具

例如
```C#
public class MyFormatProvider : IFormatProvider
{
   public object GetFormat(Type format)
   {
      if (format == typeof(ICustomFormatter))
            return new MyCustomFormatter();
      //if () ....
      //if () ....
      return null;
   }
}
```
*如此,可以提供外界通用筛选* 通常由`Tosting`,`+`号,`format`等拼接字符串的操作来调用

`.NET Framework` 包含以下三个预定义的 `IFormatProvider` 实现.

* `NumberFormatInfo`，它提供用于设置数字格式的信息
* `DateTimeFormatInfo`，它提供用于设置日期和时间格式的信息
* `CultureInfo`,表示特定区域性的类

其中`NumberFormatInfo`涉及此接口的源码如下
```C#
public Object GetFormat(Type formatType) 
{
   return formatType == typeof(NumberFormatInfo)? this: null;
}
```

## IFormattable

提供一种功能，用以将对象的值格式化为字符串表示形式

* `ToString(String, IFormatProvider)`	
  * 使用指定格式对当前实例的值设置格式。
  * String 
    * 要使用的格式。
  * IFormatProvider
    * 要用于格式化值的提供程序。

通常情况下 `IFormatProvider`, `ICustomFormatter`,`IFormattable`三者联合行动

```C#
public string ToString(string format, IFormatProvider provider)
{
   if (String.IsNullOrEmpty(format)) format = "G";//无格式则使用`G`
   if (provider == null) provider = CultureInfo.CurrentCulture;//无格式化程序的默认值

   switch (format.ToUpperInvariant())
   {
      case "G":
      case "C":
         return temp.ToString("F2", provider) + " °C";
      case "F":
         return Fahrenheit.ToString("F2", provider) + " °F";
      case "K":
         return Kelvin.ToString("F2", provider) + " K";
      default:
         throw new FormatException(String.Format("The {0} format string is not supported.", format));
   }
}
```

所谓格式,是指如下内容  

* 如果格式项不包含格式字符串 例如 `{0}`，则会将其 `null` 作为参数的值传递
* 如果格式项包含格式字符串 例如 `{0:G}`，则该格式字符串将作为参数的值传递,本例中为`G`

`ToString(String, IFormatProvider)`仅当对象未传递 `ICustomFormatter` 格式提供程序或 `IFormatProvider` 自定义格式提供程序的方法返回`null`时，复合格式设置方法才能调用该对象的实现  
等同于实现`ICustomFormatter`的提供者返回一个`((IFormattable)arg).ToString(format, CultureInfo.CurrentCulture);`返回

> `ToString(String, IFormatProvider)`方法必须支持**常规格式说明符**的 "G"  
> 此外，必须准备好处理`null`格式说明符

> 类似于`Equals`的扩种,这些接口也可以看作是`ToString`的扩充

## IObservable<T>

定义基于推送的通知的提供程序  
`IObserver<T>` 和 `IObservable<T>` 接口为基于推送的通知（也称为观察者设计模式）提供通用机制

* Subscribe(IObserver<T>)	
  * 参数
    * IObserver<T>
    * 将接收通知的对象。
  * 返回
    * IDisposable
    * 对接口的引用，它允许观察程序在提供程序完成发送通知前停止接收通知

```C#
private List<IObserver<Location>> observers;

public IDisposable Subscribe(IObserver<Location> observer)
{
   if (! observers.Contains(observer))
      observers.Add(observer);
   return new Unsubscriber(observers, observer);
}

private class Unsubscriber : IDisposable
{
   private List<IObserver<Location>>_observers;
   private IObserver<Location> _observer;

   public Unsubscriber(List<IObserver<Location>> observers, IObserver<Location> observer)
   {
      this._observers = observers;
      this._observer = observer;
   }

   public void Dispose()
   {
      if (_observer != null && _observers.Contains(_observer))
         _observers.Remove(_observer);
   }
}
```

## IObserver<T>

提供用于接收基于推送的通知的机制


* OnCompleted()	
  * 通知观察者提供程序已完成发送基于推送的通知。
* OnError(Exception)	
  * 通知观察者提供程序遇到错误情况。
* OnNext(T)	
  * 向观察者提供新数据。

```C#
using System;

public class LocationReporter : IObserver<Location>
{
   private IDisposable unsubscriber;
   private string instName;

   public LocationReporter(string name)
   {
      this.instName = name;
   }

   public string Name
   {  get{ return this.instName; } }

   public virtual void Subscribe(IObservable<Location> provider)
   {
      if (provider != null)
         unsubscriber = provider.Subscribe(this);
   }

   public virtual void OnCompleted()
   {
      Console.WriteLine("The Location Tracker has completed transmitting data to {0}.", this.Name);
      this.Unsubscribe();
   }

   public virtual void OnError(Exception e)
   {
      Console.WriteLine("{0}: The location cannot be determined.", this.Name);
   }

   public virtual void OnNext(Location value)
   {
      Console.WriteLine("{2}: The current location is {0}, {1}", value.Latitude, value.Longitude, this.Name);
   }

   public virtual void Unsubscribe()
   {
      unsubscriber.Dispose();
   }
}
```

```C#
public class LocationTracker : IObservable<Location>
{
   public LocationTracker()
   {
      observers = new List<IObserver<Location>>();
   }

   private List<IObserver<Location>> observers;

   public IDisposable Subscribe(IObserver<Location> observer)
   {
      if (! observers.Contains(observer))
         observers.Add(observer);
      return new Unsubscriber(observers, observer);
   }

   private class Unsubscriber : IDisposable
   {
      private List<IObserver<Location>>_observers;
      private IObserver<Location> _observer;

      public Unsubscriber(List<IObserver<Location>> observers, IObserver<Location> observer)
      {
         this._observers = observers;
         this._observer = observer;
      }

      public void Dispose()
      {
         if (_observer != null && _observers.Contains(_observer))
            _observers.Remove(_observer);
      }
   }

   public void TrackLocation(Nullable<Location> loc)
   {
      foreach (var observer in observers) {
         if (! loc.HasValue)
            observer.OnError(new LocationUnknownException());
         else
            observer.OnNext(loc.Value);
      }
   }

   public void EndTransmission()
   {
      foreach (var observer in observers.ToArray())
         if (observers.Contains(observer))
            observer.OnCompleted();

      observers.Clear();
   }
}
```
*以上两段代码演示了这两个接口是如何协同工作的*

## IProgress<T>

定义进度更新的提供程序

* Report(T)	
  * 报告进度更新。
  * T 进度更新之后的值

## IServiceProvider

定义用于检索服务对象的机制；也即，向其他对象提供自定义支持的对象

* object? GetService(Type)	
  * 获取指定类型的服务对象
  * 参数: 指定要获取什么对象
  * 返回: 返回指定的对象,或`null`

IServiceProvider接口由多种类型实现  
`System.Web.HttpContext` `System.ComponentModel.LicenseContext` `System.ComponentModel.MarshalByValueComponent` `System.ComponentModel.Design.ServiceContainer` 

# System.Collections中的接口

## ICollection

定义所有非泛型集合的大小、枚举数和同步方法

**属性**
* Count	
  * 获取 `ICollection` 中包含的元素数。
* IsSynchronized	
  * 获取一个值，该值指示是否同步对 `ICollection` 的访问（线程安全）。
* SyncRoot	
  * 获取可用于同步对 `ICollection` 的访问的对象。

**方法**
* CopyTo(Array, Int32)	
  * 从特定的 `ICollection` 索引开始，将 `Array` 的元素复制到一个 `Array` 中。
* GetEnumerator()	
  * 返回循环访问集合的枚举数。(继承自 `IEnumerable`)

**扩展方法**
* Cast<TResult>(IEnumerable)	
  * 将 `IEnumerable` 的元素强制转换为指定的类型。
* OfType<TResult>(IEnumerable)	
  * 根据指定类型筛选 `IEnumerable` 的元素。
* AsParallel(IEnumerable)	
  * 启用查询的并行化。
* AsQueryable(IEnumerable)	
  * 将 `IEnumerable` 转换为 `IQueryable。`

它的泛型等效项是 `System.Collections.Generic.ICollection<T>` 接口

集合本质上不是一个线程安全的,所以需要`IsSynchronized`来标志访问是同步的(接口实现者处理了多线程问题)

如果你需要处理锁,可以使用`SyncRoot`
```C#
lock(myCollection.SyncRoot)
```

`GetEnumerator`是迭代器的基础,`IEnumerator`包含整个迭代器,后面讲.

其他扩展方法都属于工具类型.

## IComparer

提供比较两个对象的方法。

* Compare(Object, Object)	
  * 比较两个对象并返回一个值，该值指示一个对象小于、等于还是大于另一个对象

一般是各种比较方法要用到此接口

返回`Int32`

一个带符号整数，指示 x 和y 的相对值：
- 如果小于 0，则 x 小于 y。
- 如果为 0，则 x 等于 y。
- 如果大于 0，则 x 大于 y。

`null`允许与任何类型进行比较，并且在使用时不会生成异常 `IComparable` 。 进行排序时， `null` 视为小于任何其他对象

> 首选的实现方法是使用 `Compare` 其中一个参数的方法.

也有一个泛型版本

> 这种泛型补充都是后来添加的,所以他们有一个自己的命名空间`System.Collections.Generic`

## IDictionary

表示键/值对的非通用集合

**属性**
* Count	
  * 获取 ICollection 中包含的元素数。(继承自 ICollection)
* IsFixedSize	
  * 获取一个值，该值指示 IDictionary 对象是否具有固定大小。
* IsReadOnly	
  * 获取一个值，该值指示 IDictionary 对象是否为只读。
* IsSynchronized	
  * 获取一个值，该值指示是否同步对 ICollection 的访问（线程安全）。(继承自 ICollection)
* Item[Object]	
  * 获取或设置具有指定键的元素。
* Keys	
  * 获取 ICollection 对象，它包含 IDictionary 对象的键。
* SyncRoot	
  * 获取可用于同步对 ICollection 的访问的对象。(继承自 ICollection)
* Values	
  * 获取 ICollection 对象，它包含 IDictionary 对象中的值。
**方法**
* Add(Object, Object)	
  * 在 IDictionary 对象中添加一个带有所提供的键和值的元素。
* Clear()	
  * 从 IDictionary 对象中移除所有元素。
* Contains(Object)	
  * 确定 IDictionary 对象是否包含具有指定键的元素。
* CopyTo(Array, Int32)	
  * 从特定的 ICollection 索引开始，将 Array 的元素复制到一个 Array 中。(继承自 ICollection)
* GetEnumerator()	
  * 返回 IDictionary 对象的 IDictionaryEnumerator 对象。
* Remove(Object)	
  * 从 IDictionary 对象中移除具有指定键的元素。
**扩展方法**
* Cast<TResult>(IEnumerable)	
  * 将 IEnumerable 的元素强制转换为指定的类型。
* OfType<TResult>(IEnumerable)	
  * 根据指定类型筛选 IEnumerable 的元素。
* AsParallel(IEnumerable)	
  * 启用查询的并行化。
* AsQueryable(IEnumerable)	
  * 将 IEnumerable 转换为 IQueryable。

## IDictionaryEnumerator

枚举非泛型字典中的元素

**属性**
* Current	
  * 获取集合中位于枚举数当前位置的元素。(继承自 IEnumerator)
* Entry	
  * 同时获取当前字典项的键和值。
* Key	
  * 获取当前字典项的键。
* Value	
  * 获取当前字典项的值。
**方法**
* MoveNext()	
  * 将枚举数推进到集合的下一个元素。(继承自 IEnumerator)
* Reset()	
  * 将枚举数设置为其初始位置，该位置位于集合中第一个元素之前。(继承自 IEnumerator)

## IEnumerable

支持简单迭代

**方法**
* GetEnumerator()	
  * 返回循环访问集合的枚举数。
**扩展方法**
* Cast<TResult>(IEnumerable)	
  * 将 IEnumerable 的元素强制转换为指定的类型。
* OfType<TResult>(IEnumerable)	
  * 根据指定类型筛选 IEnumerable 的元素。
* AsParallel(IEnumerable)	
  * 启用查询的并行化。
* AsQueryable(IEnumerable)	
  * 将 IEnumerable 转换为 IQueryable。

## IEnumerator

支持简单迭代

**属性**
* Current	
  * 获取集合中位于枚举数当前位置的元素。
**方法**
* MoveNext()	
  * 将枚举数推进到集合的下一个元素。
* Reset()	
  * 将枚举数设置为其初始位置，该位置位于集合中第一个元素之前。

*从头到尾对一个集合进行枚举在本质上不是一个线程安全的过程*

## IEqualityComparer 

定义用于支持比较对象是否相等的方法

**方法**
* Equals(Object, Object)	
  * 确定指定的对象是否相等。
* GetHashCode(Object)	
  * 返回指定对象的哈希代码。

此接口允许实现**集合**的自定义相等性比较

## IHashCodeProvider 

使用自定义哈希函数为对象提供哈希代码。

**方法**
* GetHashCode(Object)	
  * 返回指定对象的哈希代码。

其建议的替换是 `System.Collections.IEqualityComparer` 或 `System.Collections.Generic.IEqualityComparer<T>` 接口。

## IList 

**属性**
* Count	
  * 获取 ICollection 中包含的元素数。(继承自ICollection)
* IsFixedSize	
  * 获取一个值，该值指示 IList 是否具有固定大小。
* IsReadOnly	
  * 获取一个值，该值指示 IList 是否为只读。
* IsSynchronized	
  * 获取一个值，该值指示是否同步对 ICollection * 的访问（线程安全）。(继承自 ICollection)
* Item[Int32]	
  * 获取或设置指定索引处的元素。
* SyncRoot	
  * 获取可用于同步对 ICollection 的访问的对象。(继承自 ICollection)
**方法**
* Add(Object)	
  * 将某项添加到 IList 中。
* Clear()	
  * 从 IList 中移除所有项。
* Contains(Object)	
  * 确定 IList 是否包含特定值。
* CopyTo(Array, Int32)	
  * 从特定的 ICollection 索引开始，将 Array 的元* 素复制到一个 Array 中。(继承自 ICollection)
* GetEnumerator()	
  * 返回循环访问集合的枚举数。(继承自 * IEnumerable)
* IndexOf(Object)	
  * 确定 IList 中特定项的索引。
* Insert(Int32, Object)	
  * 在 IList 中的指定索引处插入一个项。
* Remove(Object)	
  * 从 IList 中移除特定对象的第一个匹配项。
* RemoveAt(Int32)	
  * 移除位于指定索引处的 IList 项。
**扩展方法**
* Cast<TResult>(IEnumerable)	
  * 将 IEnumerable 的元素强制转换为指定的类型。
* OfType<TResult>(IEnumerable)	
  * 根据指定类型筛选 IEnumerable 的元素。
* AsParallel(IEnumerable)	
  * 启用查询的并行化。
* AsQueryable(IEnumerable)	
  * 将 IEnumerable 转换为 IQueryable。

## IStructuralComparable

支持集合对象的结构化比较

**方法**
* CompareTo(Object, IComparer)	
  * 确定当前集合对象在排序顺序中的位置是位于另一个对象之前、之后还是与其位置相同

# System.Collections.Generic中的接口



# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
