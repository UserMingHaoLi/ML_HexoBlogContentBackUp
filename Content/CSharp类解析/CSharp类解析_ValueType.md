---
title: CSharp类解析_ValueType
date: 2021-06-18 22:51:00
updated: 2021-06-18 22:51:00
id: ml-20210618-225100-g143
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

继承于`Object`, 且是说有值类型的基类

无法创建直接从`ValueType`继承的类 , 但可以使用`struct `关键字来来达到效果

`ValueType` 结构通常不在代码中直接使用。 但是，它可用作方法调用中的参数，以将可能的自变量限制为值类型而不是所有对象，或者允许方法处理许多不同的值类型

```C#
public static NumericRelationship Compare(ValueType value1, ValueType value2)
```

**方法**
* Equals(Object)	
  * 指示此实例与指定对象是否相等。
* GetHashCode()	
  * 返回此实例的哈希代码。
* GetType()	
  * 获取当前实例的 Type。(继承自 Object)
* MemberwiseClone()	
  * 创建当前 Object 的浅表副本。(继承自 Object)
* ToString()	
  * 返回此实例的完全限定类型名称。


<!--more-->

# Equals(Object)	

```C#
 [System.Security.SecuritySafeCritical]
public override bool Equals (Object obj) {
   BCLDebug.Perf(false, "ValueType::Equals is not fast.  "+this.GetType().FullName+" should override Equals(Object)");
   if (null==obj) {
         return false;
   }
   RuntimeType thisType = (RuntimeType)this.GetType();
   RuntimeType thatType = (RuntimeType)obj.GetType();

   if (thatType!=thisType) {
         return false;
   }

   Object thisObj = (Object)this;
   Object thisResult, thatResult;

   // if there are no GC references in this object we can avoid reflection 
   // and do a fast memcmp
   if (CanCompareBits(this))
         return FastEqualsCheck(thisObj, obj);

   FieldInfo[] thisFields = thisType.GetFields(BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic);

   for (int i=0; i<thisFields.Length; i++) {
         thisResult = ((RtFieldInfo)thisFields[i]).UnsafeGetValue(thisObj);
         thatResult = ((RtFieldInfo)thisFields[i]).UnsafeGetValue(obj);
         
         if (thisResult == null) {
            if (thatResult != null)
               return false;
         }
         else
         if (!thisResult.Equals(thatResult)) {
            return false;
         }
   }

   return true;
}
```



# GetHashCode()	

# MemberwiseClone()	

# ToString()	

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
