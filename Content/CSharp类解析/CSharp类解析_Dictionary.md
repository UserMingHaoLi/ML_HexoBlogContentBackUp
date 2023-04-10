---
title: CSharp类解析_Dictionary
date: 2021-08-03 01:40:00
updated: 2021-08-03 01:40:00
id: ml-20210803-014000-g149
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

```CSharp
private struct Entry {
	public int hashCode;    // Lower 31 bits of hash code, -1 if unused
	public int next;        // Index of next entry, -1 if last
	public TKey key;           // Key of entry
	public TValue value;         // Value of entry
}
```
*每个Item*

```CSharp
private int[] buckets;
private Entry[] entries;
private int count;
private int version;
private int freeList;
private int freeCount;
private IEqualityComparer<TKey> comparer;
private KeyCollection keys;
private ValueCollection values;
private Object _syncRoot;
```
*基础定义*

<!--more-->

# 构造
```CSharp
this.comparer = comparer ?? EqualityComparer<TKey>.Default;
```
内容如下
```CSharp
 static readonly EqualityComparer<T> defaultComparer = CreateComparer();
 
public static EqualityComparer<T> Default {
	get {
		Contract.Ensures(Contract.Result<EqualityComparer<T>>() != null);
		return defaultComparer;
	}
}
 private static EqualityComparer<T> CreateComparer() {
	Contract.Ensures(Contract.Result<EqualityComparer<T>>() != null);

	RuntimeType t = (RuntimeType)typeof(T);
	// Specialize type byte for performance reasons
	if (t == typeof(byte)) {
		return (EqualityComparer<T>)(object)(new ByteEqualityComparer());
	}
	// If T implements IEquatable<T> return a GenericEqualityComparer<T>
	if (typeof(IEquatable<T>).IsAssignableFrom(t)) {
		return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(GenericEqualityComparer<int>), t);
	}
	// If T is a Nullable<U> where U implements IEquatable<U> return a NullableEqualityComparer<U>
	if (t.IsGenericType && t.GetGenericTypeDefinition() == typeof(Nullable<>)) {
		RuntimeType u = (RuntimeType)t.GetGenericArguments()[0];
		if (typeof(IEquatable<>).MakeGenericType(u).IsAssignableFrom(u)) {
			return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(NullableEqualityComparer<int>), u);
		}
	}

	// See the METHOD__JIT_HELPERS__UNSAFE_ENUM_CAST and METHOD__JIT_HELPERS__UNSAFE_ENUM_CAST_LONG cases in getILIntrinsicImplementation
	if (t.IsEnum) {
		TypeCode underlyingTypeCode = Type.GetTypeCode(Enum.GetUnderlyingType(t));

		// Depending on the enum type, we need to special case the comparers so that we avoid boxing
		// Note: We have different comparers for Short and SByte because for those types we need to make sure we call GetHashCode on the actual underlying type as the 
		// implementation of GetHashCode is more complex than for the other types.
		switch (underlyingTypeCode) {
			case TypeCode.Int16: // short
				return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(ShortEnumEqualityComparer<short>), t);
			case TypeCode.SByte:
				return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(SByteEnumEqualityComparer<sbyte>), t);
			case TypeCode.Int32:
			case TypeCode.UInt32:
			case TypeCode.Byte:
			case TypeCode.UInt16: //ushort
				return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(EnumEqualityComparer<int>), t);
			case TypeCode.Int64:
			case TypeCode.UInt64:
				return (EqualityComparer<T>)RuntimeTypeHandle.CreateInstanceForAnotherGenericParameter((RuntimeType)typeof(LongEnumEqualityComparer<long>), t);
		}
	}
	// Otherwise return an ObjectEqualityComparer<T>
	return new ObjectEqualityComparer<T>();
}
```
*注意`T`为`Tkey`,且对整形值类型有特殊优化*

`ObjectEqualityComparer`,可见继承了`EqualityComparer`
```CSharp
internal class ObjectEqualityComparer<T>: EqualityComparer<T>
{
[Pure]
public override bool Equals(T x, T y) {
	if (x != null) {
		if (y != null) return x.Equals(y);
		return false;
	}
	if (y != null) return false;
	return true;
}

[Pure]
public override int GetHashCode(T obj) {
	if (obj == null) return 0;
	return obj.GetHashCode();
}

internal override int IndexOf(T[] array, T value, int startIndex, int count) {
	int endIndex = startIndex + count;
	if (value == null) {
		for (int i = startIndex; i < endIndex; i++) {
			if (array[i] == null) return i;
		}
	}
	else {
		for (int i = startIndex; i < endIndex; i++) {
			if (array[i] != null && array[i].Equals(value)) return i;
		}
	}
	return -1;
}

internal override int LastIndexOf(T[] array, T value, int startIndex, int count) {
	int endIndex = startIndex - count + 1;
	if (value == null) {
		for (int i = startIndex; i >= endIndex; i--) {
			if (array[i] == null) return i;
		}
	}
	else {
		for (int i = startIndex; i >= endIndex; i--) {
			if (array[i] != null && array[i].Equals(value)) return i;
		}
	}
	return -1;
}

// Equals method for the comparer itself. 
public override bool Equals(Object obj){
	ObjectEqualityComparer<T> comparer = obj as ObjectEqualityComparer<T>;
	return comparer != null;
}        

public override int GetHashCode() {
	return this.GetType().Name.GetHashCode();
}                                
}
```

# Count Keys Values

```CSharp
public int Count {
	get { return count - freeCount; }
}
public KeyCollection Keys {
	get {
		Contract.Ensures(Contract.Result<KeyCollection>() != null);
		if (keys == null) keys = new KeyCollection(this);
		return keys;
	}
}
public ValueCollection Values {
	get {
		Contract.Ensures(Contract.Result<ValueCollection>() != null);
		if (values == null) values = new ValueCollection(this);
		return values;
	}
}
```
*所谓`KeyCollection`和`ValueCollection`都只是`MoveNext`的时候只取其中的`Key`或`Value`*

# Add Insert

```CSharp
public void Add(TKey key, TValue value) {
	Insert(key, value, true);
}
private void Insert(TKey key, TValue value, bool add) {

	if( key == null ) {
		ThrowHelper.ThrowArgumentNullException(ExceptionArgument.key);
	}

	if (buckets == null) Initialize(0);
	int hashCode = comparer.GetHashCode(key) & 0x7FFFFFFF;
	int targetBucket = hashCode % buckets.Length;

#if FEATURE_RANDOMIZED_STRING_HASHING
	int collisionCount = 0;
#endif

	for (int i = buckets[targetBucket]; i >= 0; i = entries[i].next) {
		if (entries[i].hashCode == hashCode && comparer.Equals(entries[i].key, key)) {
			if (add) { 
				ThrowHelper.ThrowArgumentException(ExceptionResource.Argument_AddingDuplicate);
			}
			entries[i].value = value;
			version++;
			return;
		} 

#if FEATURE_RANDOMIZED_STRING_HASHING
		collisionCount++;
#endif
	}
	int index;
	if (freeCount > 0) {
		index = freeList;
		freeList = entries[index].next;
		freeCount--;
	}
	else {
		if (count == entries.Length)
		{
			Resize();
			targetBucket = hashCode % buckets.Length;
		}
		index = count;
		count++;
	}

	entries[index].hashCode = hashCode;
	entries[index].next = buckets[targetBucket];
	entries[index].key = key;
	entries[index].value = value;
	buckets[targetBucket] = index;
	version++;

#if FEATURE_RANDOMIZED_STRING_HASHING

#if FEATURE_CORECLR
	// In case we hit the collision threshold we'll need to switch to the comparer which is using randomized string hashing
	// in this case will be EqualityComparer<string>.Default.
	// Note, randomized string hashing is turned on by default on coreclr so EqualityComparer<string>.Default will 
	// be using randomized string hashing

	if (collisionCount > HashHelpers.HashCollisionThreshold && comparer == NonRandomizedStringEqualityComparer.Default) 
	{
		comparer = (IEqualityComparer<TKey>) EqualityComparer<string>.Default;
		Resize(entries.Length, true);
	}
#else
	if(collisionCount > HashHelpers.HashCollisionThreshold && HashHelpers.IsWellKnownEqualityComparer(comparer)) 
	{
		comparer = (IEqualityComparer<TKey>) HashHelpers.GetRandomizedEqualityComparer(comparer);
		Resize(entries.Length, true);
	}
#endif // FEATURE_CORECLR

#endif

}
```
*和`HashTbl`类似,也是双倍素数和` 0x7FFFFFFF`*

> 其中7FFFFFFF等同于八进制的慢15位,也就是intMax

且使用`buckets`为索引,所以操作速度较快, 但是只对于`Key`生效`ContainsValue(TValue value)`还是全部遍历

后面就是一堆迭代器和包装, 没啥好说的.

----

对于容器规则,找了两张图片方便理解  
水印就不去了.

![Dictionary_Image1]
![Dictionary_Image1]

====

最后,使用以下数列内容作为初始容量.可略微节省性能(算素数有开销)

```CSharp
public static readonly int[] primes = {
		3, 7, 11, 17, 23, 29, 37, 47, 59, 71, 89, 107, 131, 163, 197, 239, 293, 353, 431, 521, 631, 761, 919,
		1103, 1327, 1597, 1931, 2333, 2801, 3371, 4049, 4861, 5839, 7013, 8419, 10103, 12143, 14591,
		17519, 21023, 25229, 30293, 36353, 43627, 52361, 62851, 75431, 90523, 108631, 130363, 156437,
		187751, 225307, 270371, 324449, 389357, 467237, 560689, 672827, 807403, 968897, 1162687, 1395263,
		1674319, 2009191, 2411033, 2893249, 3471899, 4166287, 4999559, 5999471, 7199369};
```

```CSharp
for (int i = (min | 1); i < Int32.MaxValue;i+=2) 
{
	if (IsPrime(i) && ((i - 1) % Hashtable.HashPrime != 0))
		return i;
}
```
额外条件中的`Hashtable.HashPrime = 101`  
这是为了实现`"均匀分布式"键`,也就是让结果更稀疏,均匀


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
[Dictionary_Image1]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/CSharp%E7%B1%BB%E8%A7%A3%E6%9E%90/CSharp%E7%B1%BB%E8%A7%A3%E6%9E%90_Dictionary/Dictionary_Image1.jpg?raw=true "Dictionary_Image1"
[Dictionary_Image2]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/CSharp%E7%B1%BB%E8%A7%A3%E6%9E%90/CSharp%E7%B1%BB%E8%A7%A3%E6%9E%90_Dictionary/Dictionary_Image2.jpg?raw=true "Dictionary_Image2"
<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
