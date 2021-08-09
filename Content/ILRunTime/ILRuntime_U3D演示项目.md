---
title: ILRuntime_U3D演示项目
date: 2021-08-06 00:35:00
updated: 2021-08-06 00:35:00
id: ml-20210803-014000-g150
categories:
	- ILRuntime
tags: 
	- ILRuntime
---

以下链接通往该项目的GitHub

[GitHub_ILRuntime][GitHub_ILRuntime_Link]  
[GitHub_ILRuntimeU3D][GitHub_ILRuntime_Link]

<!--more-->

# HotFix和Unity

其中Unity不能热更,HotFix可以热更,是一个额外的项目工程,最后会编译为Dll方便运行时加载  

> 以`~`结尾的工程不会被Unity捕获,所以放在项目里面需要手动编译.

# 使用

> 如果你使用的是`中国特别版Unity`，那直接打开`Package Manager`即可找到`ILRuntime`  
> 如果你使用的是`国际版Unity`，或者无法在`PackageManager`中找到`ILRuntime`，则需要按照以下步骤设置项目

首先需要在项目的`Packages/manifest.json`中，添加`ILRuntime`的源信息，在这个文件的`dependencies`节点前增加以下代码

```json
"scopedRegistries": [
  {
    "name": "ILRuntime",
    "url": "https://registry.npmjs.org",
    "scopes": [
      "com.ourpalm"
    ]
  }
],
```

然后通过`Unity`的`Window->Package Manager`菜单，打开`Package Manager`，将上部标签页选项选择为`All Packages`，`Advanced`里勾上`Show Preview Packages`，等待`Unity`加载完包信息，应该就能在左侧列表中找到`ILRuntime`，点击安装即可

部分`Unity`版本可以无法直接在列表中刷出`ILRuntime`，如果左边列表找不着，那就在项目的`manifest.json`中的`dependencies`段的开头，增加如下代码手动将`ILRuntime`添加进项目

```json
"com.ourpalm.ilruntime": "1.6.0",
```

`ILRuntime`包安装完毕后，在`Package Manager`中选中`ILRuntime`， 右边详细页面中有`Samples`，点击右方的`Import to project`可以将`ILRuntime`的示例`Demo`直接导入当前工程。

# 委托适配器

如果委托只在ILRuntime域中运行,那么无需注册.   
如果任何委托需要前往域外,则需要注册

```C#
//Action
appDomain.DelegateManager.RegisterMethodDelegate<int, float>();
//Function
appDomain.DelegateManager.RegisterFunctionDelegate<int, float, bool>();
```

`ILRuntime`内部是使用`Action`,`Func`这两个系统自带委托类型来生成的委托实例，所以如果你需要将一个不是`Action`或者`Func`类型的委托实例传到`ILRuntime`外部使用的话，除了委托适配器，还需要额外写一个转换器，将`Action`和`Func`转换成你真正需要的那个委托类型

```C#
app.DelegateManager.RegisterDelegateConvertor<SomeFunction>((action) =>
{
    return new SomeFunction((a, b) =>
    {
       return ((Func<int, float, bool>)action)(a, b);
    });
});
```

> 为了避免不必要的麻烦，以及后期热更出现问题，建议项目遵循以下几点：

* 尽量避免不必要的跨域委托调用  
* 尽量使用Action以及Func这两个系统内置万用委托类型

# 跨域继承

如果你想在`热更DLL`项目当中`继承一个`Unity主工程`里的类`，或者`实现一个主工程里的接口`，你需要在`Unity主工程`中实现一个继承适配器

```C#
appdomain.RegisterCrossBindingAdaptor(new ClassInheritanceAdaptor());
```

```C#
public class TestClass2Adapter : CrossBindingAdaptor
{
	//定义访问方法的方法信息
	static CrossBindingMethodInfo mVMethod1_0 = new CrossBindingMethodInfo("VMethod1");
	static CrossBindingFunctionInfo<System.Boolean> mVMethod2_1 = new CrossBindingFunctionInfo<System.Boolean>("VMethod2");
	static CrossBindingMethodInfo mAbMethod1_3 = new CrossBindingMethodInfo("AbMethod1");
	static CrossBindingFunctionInfo<System.Int32, System.Single> mAbMethod2_4 = new CrossBindingFunctionInfo<System.Int32, System.Single>("AbMethod2");
	public override Type BaseCLRType
	{
		get
		{
			return typeof(ILRuntimeTest.TestFramework.TestClass2);//这里是你想继承的类型
		}
	}

	public override Type AdaptorType
	{
		get
		{
			return typeof(Adapter);
		}
	}

	public override object CreateCLRInstance(ILRuntime.Runtime.Enviorment.AppDomain appdomain, ILTypeInstance instance)
	{
		return new Adapter(appdomain, instance);
	}

	public class Adapter : ILRuntimeTest.TestFramework.TestClass2, CrossBindingAdaptorType
	{
		ILTypeInstance instance;
		ILRuntime.Runtime.Enviorment.AppDomain appdomain;

		//必须要提供一个无参数的构造函数
		public Adapter()
		{

		}

		public Adapter(ILRuntime.Runtime.Enviorment.AppDomain appdomain, ILTypeInstance instance)
		{
			this.appdomain = appdomain;
			this.instance = instance;
		}

		public ILTypeInstance ILInstance { get { return instance; } }

		//下面将所有虚函数都重载一遍，并中转到热更内
		public override void VMethod1()
		{
			if (mVMethod1_0.CheckShouldInvokeBase(this.instance))
				base.VMethod1();
			else
				mVMethod1_0.Invoke(this.instance);
		}

		public override System.Boolean VMethod2()
		{
			if (mVMethod2_1.CheckShouldInvokeBase(this.instance))
				return base.VMethod2();
			else
				return mVMethod2_1.Invoke(this.instance);
		}

		protected override void AbMethod1()
		{
			mAbMethod1_3.Invoke(this.instance);
		}

		public override System.Single AbMethod2(System.Int32 arg1)
		{
			return mAbMethod2_4.Invoke(this.instance, arg1);
		}

		public override string ToString()
		{
			IMethod m = appdomain.ObjectType.GetMethod("ToString", 0);
			m = instance.Type.GetVirtualMethod(m);
			if (m == null || m is ILMethod)
			{
				return instance.ToString();
			}
			else
				return instance.Type.FullName;
		}
	}
}
```

# 反射

默认情况下，`System.Reflection`命名空间中的方法，并不可能得知`ILRuntime`中定义的类型，因此无法通过`Type.GetType`等接口取得`热更DLL`里面的类型

为了解决这个问题，ILRuntime额外实现了几个用于反射的辅助类：`ILRuntimeType`，`ILRuntimeMethodInfo`，`ILRuntimeFieldInfo`等

# 通过反射获取Type

在`热更DLL`当中，直接调用`Type.GetType(“TypeName”)`或者`typeof(TypeName)`均可以得到有效`System.Type`类型实例

在`Unity主工程`中，无法通过`Type.GetType`来取得`热更DLL`内部定义的类，而只能通过以下方式得到`System.Type`实例

```C#
IType type = appdomain.LoadedTypes["TypeName"];
Type t = type.ReflectedType;
```

# 通过反射创建实例

在`热更DLL`当中，可以直接通过`Activator`来创建实例

```C#
Type t = Type.GetType("TypeName");//或者typeof(TypeName)
//以下两种方式均可以
object instance = Activator.CreateInstance(t);
object instance = Activator.CreateInstance<TypeName>();
```

在`Unity主工程`中，无法通过`Activator`来创建`热更DLL`内类型的实例，必须通过`AppDomain`来创建实例
```C#
object instance = appdomain.Instantiate("TypeName");
```

# 通过反射调用方法

在`热更DLL`当中，通过反射调用方法跟通常C#用法没有任何区别

```C#
Type type = typeof(TypeName);
object instance = Activator.CreateInstance(type);
MethodInfo mi = type.GetMethod("foo");
mi.Invoke(instance, null);
```

在`Unity主工程`中，可以通过C#通常用法来调用，也可以通过ILRuntime自己的接口来调用，两个方式是等效的

```C#
IType t = appdomain.LoadedTypes["TypeName"];
Type type = t.ReflectedType;

object instance = appdomain.Instantiate("TypeName");

//系统反射接口
MethodInfo mi = type.GetMethod("foo");
mi.Invoke(instance, null);

//ILRuntime的接口
IMethod m = t.GetMethod("foo", 0);
appdomain.Invoke(m, instance, null);
```

# 通过反射获取和设置Field的值

没有区别

```C#
Type t;
FieldInfo fi = t.GetField("field");
object val = fi.GetValue(instance);
fi.SetValue(instance, val);
```

# 通过反射获取Attribute标注

没有区别

```C#
Type t;
FieldInfo fi = t.GetField("field");
object[] attributeArr = fi.GetCustomAttributes(typeof(SomeAttribute), false);
```

# 注意

> **在`Unity主工程`中不能通过`new T()`的方式来创建热更工程中的类型实例**

# 带参数的方法的重定向

展示怎么通过CLR重定向来实现在`Debug.Log`调用中打印热更DLL中的调用堆栈

```C#
public unsafe static StackObject* DLog(ILIntepreter __intp, StackObject* __esp, List<object> __mStack, CLRMethod __method, bool isNewObj)
{
    ILRuntime.Runtime.Enviorment.AppDomain __domain = __intp.AppDomain;
    StackObject* ptr_of_this_method;
    //只有一个参数，所以返回指针就是当前栈指针ESP - 1
    StackObject* __ret = ILIntepreter.Minus(__esp, 1);
    //第一个参数为ESP -1， 第二个参数为ESP - 2，以此类推
    ptr_of_this_method = ILIntepreter.Minus(__esp, 1);
    //获取参数message的值
    object message = StackObject.ToObject(ptr_of_this_method, __domain, __mStack);
    //需要清理堆栈
    __intp.Free(ptr_of_this_method);
    //如果参数类型是基础类型，例如int，可以直接通过int param = ptr_of_this_method->Value获取值，
    //关于具体原理和其他基础类型如何获取，请参考ILRuntime实现原理的文档。
			
    //通过ILRuntime的Debug接口获取调用热更DLL的堆栈
    string stackTrace = __domain.DebugService.GetStackTrance(__intp);
    Debug.Log(string.Format("{0}\n{1}", format, stackTrace));

    return __ret;
}
//重定向
appdomain.RegisterCLRMethodRedirection(typeof(Debug).GetMethod("Log"), DLog);
```

> 一定要记得将`CLR绑定`的注册写在`CLR重定向`的注册后面，因为同一个方法只能被重定向一次，只有先注册的那个才能生效

```C#
//注册方法如下
ILRuntime.Runtime.Generated.CLRBindings.Initialize(appdomain);
```

# Json

Json序列化是开发中非常经常需要用到的功能，考虑到其通用性，因此ILRuntime对`LitJson`这个序列化库进行了集成

在ILRuntime初始化阶段，在注册CLR绑定之前，执行下面这行代码

```C#
LitJson.JsonMapper.RegisterILRuntimeCLRRedirection(appdomain);
```

```C#
string json = JsonMapper.ToJson(obj);
JsonTestClass obj = JsonMapper.ToObject<JsonTestClass>(json);
```

# iOS IL2CPP打包注意事项

## 类型裁剪

IL2CPP在打包时会自动对Unity工程的DLL进行裁剪，将代码中没有引用到的类型裁剪掉，以达到减小发布后ipa包的尺寸的目的

Assets目录中建立一个叫link.xml的XML文件，然后按照下面的格式指定你需要保留的类型

```XML
<linker>
  <assembly fullname="UnityEngine" preserve="all"/>
  <assembly fullname="Assembly-CSharp">
    <namespace fullname="MyGame.Utils" preserve="all"/>
    <type fullname="MyGame.SomeClass" preserve="all"/>
  </assembly>  
</linker>
```

## 泛型

每个泛型实例实际上都是一个独立的类型，`List<A>` 和 `List<B>`是两个完全没有关系的类型

在ILRuntime中解决这个问题有两种方式

一个是使用CLR绑定，把用到的泛型实例都进行CLR绑定  
另外一个方式是在Unity主工程中，建立一个类，然后在里面定义用到的那些泛型实例的public变量。这两种方式都可以告诉IL2CPP保留这个类型的代码供运行中使用

> 因此建议大家在实际开发中，尽量使用热更DLL内部的类作为泛型参数  

因为DLL内部的类型都是ILTypeInstance  
此外如果泛型模版类就是在DLL里定义的的话，那就完全不需要进行任何处理

### 泛型方法

跟泛型实例一样

尝试在Unity的主工程中随便写一个static的方法，然后对这个泛型方法调用一下即可，这个方法无需被调用，只是用来告诉IL2CPP我们需要这个方法

# ILRuntime的性能优化建议

Release模式会比Debug模式的性能高至少2倍

关闭Development Build选项来发布Unity项目

## CLR绑定

默认情况下，ILRuntime中调用Unity主工程的方法，ILRuntime会通过反射对目标方法进行调用

## 值类型

由于值类型的特殊和ILRuntime的实现原理，使用ILRuntime外部定义的值类型（例如UnityEngine.Vector3）在默认情况下会造成额外的装箱拆箱开销，以及相对应的GC Alloc内存分配。

通过对这些值类型添加绑定器，可以大幅增加值类型的执行效率

# 接口调用建议

为了调用方便，ILRuntime的很多接口使用了params可变参数，但是有可能会无意间忽视这个操作带来的GCAlloc

```C#
appdomain.Invoke("MyGame.Main", "Initialize", null);
appdomain.Invoke("MyGame.Main", "Start", null, 100, 200);
```

这两个操作在调用的时候，会分别生成一个`object[0]`和`object[2]`，从而产生GC Alloc

如果你需要在Update等性能关键的地方调用热更DLL中的方法，应该按照以下方式缓存这个参数数组

```C#
object[] param0 = new object[0];
object[] param2 = new object[2];
IMethod m, m2;

void Start()
{
    m = appdomain.LoadedTypes["MyGame.SomeUI"].GetMethod("Update", 0);
	m2 = appdomain.LoadedTypes["MyGame.SomeUI"].GetMethod("SomethingAfterUpdate", 2);
}

void Update()
{
    appdomain.Invoke(m, null, param0);
	param2[0] = this;
	param2[1] = appdomain;
	appdomain.Invoke(m2, null, param2);
}
```

如果需要传递的参数或返回值中包含int, float等基础类型，那使用上面的方法依然无法消除GC Alloc，为了更高效率的调用，ILRuntime提供了`InvocationContext`这种调用方式，需要按照如下方式调用

```C#
int result = 0;
using(var ctx = appdomain.BeginInvoke(m))
{
    //依次将参数压入栈，如果为成员方法，第一个参数固定为对象实例
    ctx.PushObject(this);
	ctx.PushInteger(123);
	//开始调用
	ctx.Invoke();
	//调用完毕后使用对应的Read方法获取返回值
	result = ctx.ReadInteger();
}
```

# 我要怎么才能在Profiler里看见热更内的方法耗时情况呢？

```C#
//在ILRuntime的初始化处加入以下代码即可在Profiler中看见热更内方法的耗时情况，无需开启DeepProfile，真机上也可使用
#if DEBUG && (UNITY_EDITOR || UNITY_ANDROID || UNITY_IPHONE)
        //由于Unity的Profiler接口只允许在主线程使用，为了避免出异常，需要告诉ILRuntime主线程的线程ID才能正确将函数运行耗时报告给Profiler
        appdomain.UnityMainThreadID = System.Threading.Thread.CurrentThread.ManagedThreadId;
#endif
```

# 真机上调试或运行时出现随机闪退

```C#
请确认XCode的编译选项中是否使用了Debug，由于iPhone的线程栈空间很小，调用层级稍微深一点就会出现爆栈，因此请使用Release选项编译XCode工程
```

# 使用ILRuntime后产生的性能问题

是否在热更代码中使用了`foreach`，由于原理限制，在热更中使用`foreach`无法避免产生GC Alloc

由于热更都是解译执行，所以执行效率跟直接执行天然就有20-100倍的差距，因此不大适合需要进行大量遍历计算的操作，可将耗时的工具方法放入主工程辅助

# 总结

* 委托
* 继承
* 反射
* 值类型
  * 传递时会`New Object[]`
* Foreach
* Debug和Release

一般来说,`HotFix`调用`Unity`都是没啥问题的,只要注意委托外传和继承   
但是`Unity`调用`HotFix`就很难搞了

## 委托传往域外

将`HotFix`的委托传给`System`,`UnityEngine`,`主工程`都要注册,且不是`Action`,`Func`都要再额外进行转化

## 跨域继承

如果你想在`热更DLL`项目当中`继承一个`Unity主工程`里的类`. 那么需要写一个适配器

## 反射

`HotFix`是无法正常反射的, 需要使用框架自带的一些方法拿反射

# 单词

`ILRuntime`其中`T`不用大写.

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
<!-- 链接 -->

[GitHub_ILRuntime_Link]:https://github.com/Ourpalm/ILRuntime "GitHub上的ILRuntime"
[GitHub_ILRuntimeU3D_Link]:https://github.com/Ourpalm/ILRuntimeU3D "GitHub上的ILRuntimeU3D"
<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
