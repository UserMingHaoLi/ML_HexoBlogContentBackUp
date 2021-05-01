---
title: CSharp编程指南_运算符和表达式
date: 2021-04-29 01:15:00
updated: 2021-04-29 01:15:00
id: ml-20210429-011500-g134
categories:
	- 基础
	- CSharp编程指南
tags: 
	- 基础
	- CSharp编程指南
	- CSharp
---

`C#` 提供了许多运算符。 其中许多都受到内置类型的支持，可用于对这些类型的值执行基本操作

* `算术运算符`，将对数值操作数执行算术运算
* `比较运算符`，将比较数值操作数
* `布尔逻辑运算符`，将对 bool 操作数执行逻辑运算
* `位运算符和移位运算符`，将对整数类型的操作数执行位运算或移位运算
* `相等运算符`，将检查其操作数是否相等

通常可以重载这些运算符，也就是说，可以为用户定义类型的操作数指定运算符行为。

下表按最高优先级到最低优先级的顺序列出 C# 运算符。 每行中运算符的优先级相同  
相同就从左到右计算.

<!--more-->

| 运算符                                                                                                                           | 类别或名称                  |
| -------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| x.y、f(x)、a[i]、x?.y、x?[y]、x++、x--、x!、new、typeof、checked、unchecked、default、nameof、delegate、sizeof、stackalloc、x->y | 基本                        |
| +x、-x、!x、~x、++x、--x、^x、(T)x、await、&x、*x、true 和 false                                                                 | 一元                        |
| x..y                                                                                                                             | 范围                        |
| switch	switch                                                                                                                    | 表达式                      |
| with                                                                                                                             | with 表达式                 |
| x * y、x / y、x % y                                                                                                              | 乘法                        |
| x + y、x – y                                                                                                                     | 加法                        |
| x << y, x >> y                                                                                                                   | 移位                        |
| x < y, x > y、x <= y, x >= y、is、as                                                                                             | 关系和类型测试              |
| x == y、x != y                                                                                                                   | 相等                        |
| x & y                                                                                                                            | 布尔逻辑 AND 或按位逻辑 AND |
| x ^ y                                                                                                                            | 布尔逻辑 XOR 或按位逻辑 XOR |
| `x | y`                                                                                                                          | 布尔逻辑 OR 或按位逻辑 OR   |
| x && y                                                                                                                           | 条件“与”                    |
| `x || y`                                                                                                                         | 条件“或”                    |
| x ?? y                                                                                                                           | Null 合并运算符             |
| c ? t : f                                                                                                                        | 条件运算符                  |
| x = y、x += y、x -= y、x *= y、x /= y、x %= y、x &= y、`x |= y`、x ^= y、x <<= y、x >>= y、x ??= y、=>                           | 赋值和 lambda 声明          |


# 运算符结合性

当运算符的优先级相同，运算符的结合性决定了运算的执行顺序

左结合运算符按从左到右的顺序计算。 除`赋值运算符`和 `null` 合并运算符外，所有**二元运算符**都是左结合运算符。 例如，`a + b - c` 将计算为 `(a + b) - c`

右结合运算符按从右到左的顺序计算。 `赋值运算符`、`null 合并运算符`和`条件运算符?:`是右结合运算符。 例如，`x = y = z` 将计算为 `x = (y = z)`

> 所以使用括号是最好的选择.

# 操作数计算

与运算符的优先级和结合性无关，从左到右计算表达式中的操作数。 以下示例展示了运算符和操作数的计算顺序

| 表达式          | 计算顺序            |
| --------------- | ------------------- |
| a + b           | a, b, +             |
| a + b * c       | a, b, c, *, +       |
| a / b + c * d   | a, b, /, c, d, *, + |
| a / (b + c) * d | a, b, c, +, /, d, * |

> 扩展阅读,**逆波兰**

通常，会计算所有运算符操作数  
但是，某些运算符有条件地计算操作数  
此类运算符的最左侧操作数的值定义了是否应计算其他操作数

如:`AND (&&)` 和 `OR (||) `  
`null 合并运算符 ??` 和 `??=`、`null 条件运算符 ?.` 和 `?[]` 以及条件运算符`?:`

# 算术运算符

以下运算符对数值类型的操作数执行算术运算：
一元 `++（增量）`、`--（减量）`、`+（加）`和 `-（减）`运算符
二元 `*（乘法）`、`/（除法）`、`%（余数）`、`+（加法）`和 `-（减法）`运算符

所有整型和浮点数值类型都支持这些运算符

对于整型类型，这些运算符（除 `++` 和 `--` 运算符以外）是为 `int`、`uint`、`long` 和 `ulong` 类型定义的  
如果操作数都是其他整型类型（sbyte、byte、short、ushort 或 char），它们的值将转换为 `int` 类型

如果操作数是不同的整型类型或浮点类型，它们的值将转换为最接近的包含类型（如果存在该类型）

`++` 和 `--` 运算符是为所有整型和浮点数值类型以及 char 类型定义的

## 增量运算符 ++

一元增量运算符 `++` 按 `1` 递增其操作数

增量运算符以两种形式进行支持：后缀增量运算符 `x++` 和前缀增量运算符 `++x`

### 后缀递增运算符

`x++` 的结果是此操作前的 `x` 的值
```C#
int i = 3;
Console.WriteLine(i);   // output: 3
Console.WriteLine(i++); // output: 3
Console.WriteLine(i);   // output: 4
```

### 前缀增量运算符

`++x` 的结果是此操作后的 `x` 的值
```C#
double a = 1.5;
Console.WriteLine(a);   // output: 1.5
Console.WriteLine(++a); // output: 2.5
Console.WriteLine(a);   // output: 2.5
```

## 减量运算符 --

同`++`,就是替换为了`--`,效果也编程了`-1`

### 后缀递减运算符

```C#
int i = 3;
Console.WriteLine(i);   // output: 3
Console.WriteLine(i--); // output: 3
Console.WriteLine(i);   // output: 2
```

### 前缀减量运算符

```C#
double a = 1.5;
Console.WriteLine(a);   // output: 1.5
Console.WriteLine(--a); // output: 0.5
Console.WriteLine(a);   // output: 0.5
```

> **注意,这些`++`,`--`运算都不是线程安全的**

## 一元加和减运算符

一元 `+` 运算符返回其操作数的值。 一元 `-` 运算符对其操作数的数值取负  
也就是正号和负号

```C#
Console.WriteLine(+4);     // output: 4
Console.WriteLine(-4);     // output: -4
Console.WriteLine(-(-4));  // output: 4
uint a = 5;
var b = -a;
Console.WriteLine(b);            // output: -5
Console.WriteLine(b.GetType());  // output: System.Int64
Console.WriteLine(-double.NaN);  // output: NaN
```

## 乘法运算符 *

乘法运算符 `*` 计算其操作数的乘积
```C#
Console.WriteLine(5 * 2);         // output: 10
Console.WriteLine(0.5 * 2.5);     // output: 1.25
Console.WriteLine(0.1m * 23.4m);  // output: 2.34
```

## 除法运算符 /

除法运算符 `/` 用它的左侧操作数除以右侧操作数

### 整数除法

对于整数类型的操作数，`/` 运算符的**结果为整数类型**，并且等于两个操作数之商**向零舍入**后的结果

```C#
Console.WriteLine(13 / 5);    // output: 2
Console.WriteLine(-13 / 5);   // output: -2
Console.WriteLine(13 / -5);   // output: -2
Console.WriteLine(-13 / -5);  // output: 2
```

> 若要得出浮点结果,则需要其中至少一个数为浮点类型.

### 浮点除法

对于 `float`、`double` 和 `decimal` 类型，`/` 运算符的结果为两个操作数之商

```C#
Console.WriteLine(16.8f / 4.1f);   // output: 4.097561
Console.WriteLine(16.8d / 4.1d);   // output: 4.09756097560976
Console.WriteLine(16.8m / 4.1m);   // output: 4.0975609756097560975609756098
```

*如果操作数之一为 `decimal`，那么另一个操作数不得为 `float` 和 `double`，因为 `float` 和 `double` 都无法隐式转换为 `decimal`*

> 使用 `Math.DivRem` 方法计算整数除法和余数结果

## 浮点余数

```C#
Console.WriteLine(-5.2f % 2.0f); // output: -1.2
Console.WriteLine(5.9 % 3.1);    // output: 2.8
Console.WriteLine(5.9m % 3.1m);  // output: 2.8
```

> 如果需要符合 `IEEE 754` 规范的余数运算，请使用 `Math.IEEERemainder`

## 加法运算符 +

## 减法运算符 -

略,不水了.

## 复合赋值

即以上运算符的`=`复合模式  

```C#
x op= y
```
*其中`op`为上面的运算符*

等效于
```C#
x = x op y
```

例子
```C#
int a = 5;
a += 9;
Console.WriteLine(a);  // output: 14
a -= 4;
Console.WriteLine(a);  // output: 10
a *= 2;
Console.WriteLine(a);  // output: 20
a /= 4;
Console.WriteLine(a);  // output: 5
a %= 3;
Console.WriteLine(a);  // output: 2
```

如果 `op` 是预定义的运算符并且运算的结果可以显式转换为 `x` 的类型 T,则等效于 `x = (T)(x op y)`

> 还可以使用 `+=` 和 `-=` 运算符分别订阅和取消订阅事件

## 运算符优先级和关联性

查开头的表.

由高到低如下

* 后缀增量 `x++` 和减量 `x--` 运算符
* 前缀增量 `++x` 和减量 `--x` 以及一元 `+` 和 `-` 运算符
* 乘法 `*`、`/` 和 `%` 运算符
* 加法 `+` 和 `-` 运算符

## 算术溢出和被零除

### 整数算术溢出

整数被零除总是引发 `DivideByZeroException`

溢出检查上下文  

* 在已检查的上下文中，如果在常数表达式中发生溢出，则会发生编译时错误。 否则，当在运行时执行此运算时，则引发 `OverflowException`
* 在未检查的上下文中，会通过丢弃任何不适应目标类型的高序位来将结果截断

### 浮点运算溢出

使用 `float` 和 `double` 类型的算术运算永远不会引发异常。 使用这些类型的算术运算的结果可能是表示无穷大和非数字的特殊值之一

```C#
double a = 1.0 / 0.0;
Console.WriteLine(a);                    // output: Infinity
Console.WriteLine(double.IsInfinity(a)); // output: True
Console.WriteLine(double.MaxValue + double.MaxValue); // output: Infinity
double b = 0.0 / 0.0;
Console.WriteLine(b);                // output: NaN
Console.WriteLine(double.IsNaN(b));  // output: True
```

对于 `decimal` 类型的操作数，算术溢出始终会引发 `OverflowException`，并且被零除始终会引发 `DivideByZeroException`

## 舍入误差

由于实数和浮点运算的浮点表达形式的常规限制，在使用浮点类型的计算中可能会发生舍入误差

```C#
Console.WriteLine(.41f % .2f); // output: 0.00999999

double a = 0.1;
double b = 3 * a;
Console.WriteLine(b == 0.3);   // output: False
Console.WriteLine(b - 0.3);    // output: 5.55111512312578E-17
decimal c = 1 / 3.0m;
decimal d = 3 * c;
Console.WriteLine(d == 1.0m);  // output: False
Console.WriteLine(d);          // output: 0.9999999999999999999999999999
```

> 有关详细信息，请参阅 `System.Double`、`System.Single` 或 `System.Decimal` 参考页上的注解

## 运算符可重载性

用户定义类型可以重载一元（`++`、`--`、`+` 和 `-`）和二元（`*`、`/`、`%`、`+` 和 `-`）算术运算符

重载二元运算符时，对应的复合赋值运算符也会隐式重载。 用户定义类型不能显式重载复合赋值运算符

# 布尔逻辑运算符(Boolean)

以下运算符使用 `bool` 操作数执行逻辑运算

* 一元 `!（逻辑非）`运算符。
* 二元 `&（逻辑与）`、`|（逻辑或）`和 `^（逻辑异或）`运算符。 这些运算符*始终计算两个操作数*。
* 二元 `&&（条件逻辑与）`和 `||（条件逻辑或）`运算符。 这些运算符仅在*必要时才计算右侧操作数*

## 逻辑非运算符 !

如果操作数的计算结果为 `false`，它生成 `true`；如果操作数的计算结果为 `true`，它生成 `false`

```C#
bool passed = false;
Console.WriteLine(!passed);  // output: True
Console.WriteLine(!true);    // output: False
```

> 从 `C# 8.0` 起，一元后缀 `!` 运算符为 `null` 包容运算符

## 逻辑 AND 运算符 &

如果 `x` 和 `y` 的计算结果都为 `true`，则 `x & y` 的结果为 `true`。 否则，结果为 `false`

这些运算符*始终计算两个操作数*

```C#
bool SecondOperand()
{
    Console.WriteLine("Second operand is evaluated.");
    return true;
}
bool a = false & SecondOperand();
Console.WriteLine(a);
// Output:
// Second operand is evaluated.
// False
bool b = true & SecondOperand();
Console.WriteLine(b);
// Output:
// Second operand is evaluated.
// True
```

* 对于`&&`但如果左侧操作数的计算结果为 `false`，它就不会计算右侧操作数*

## 逻辑异或运算符 ^

如果 `x` 计算结果为 `true` 且 `y` 计算结果为 `false`，或者 `x` 计算结果为 `false` 且 `y` 计算结果为 `true`，那么 `x ^ y` 的结果为 `true`  
否则为`false`

```C#
Console.WriteLine(true ^ true);    // output: False
Console.WriteLine(true ^ false);   // output: True
Console.WriteLine(false ^ true);   // output: True
Console.WriteLine(false ^ false);  // output: False
```

## 逻辑或运算符 |

如果 `x` 或 `y` 的计算结果为 `true`，则 `x` | `y` 的结果为 `true`。 否则，结果为 `false`

这个也是始终计算两个操作数,想要短路计算,需要使用`||`

## 条件逻辑 AND 运算符 &&

就是之前`&`的短路算法,如果的到一个`false`就不会继续运算了,因为无论怎么运算,结果都是`false`

> 所以在只需求结果的情况下好用,如果一定需要触发两个操作,则需使用`&`

## 条件逻辑或运算符 ||

同上,同`|`

## 可以为 null 的布尔逻辑运算符

麻了,如果你一定要用这个`bool?`  
查表吧,看文章都是坑爹呢.

## 复合赋值

同之前的`op=`  
逻辑运算符也可写作`|=`,`&=`,`^=`

`x op= y`等效于`x = x op y`

## 运算符优先级

以下列表按优先级从高到低的顺序对逻辑运算符进行排序

* 逻辑与运算符 `&`
* 逻辑非运算符 `!`
* 逻辑异或运算符 `^`
* 逻辑或运算符 `|`
* 条件逻辑与运算符 `&&`
* 条件逻辑或运算符 `||`

## 运算符可重载性

用户定义类型可以重载`!`、`&`、`|` 和 `^` 运算符。 重载二元运算符时，对应的复合赋值运算符也会隐式重载。 用户定义类型不能显式重载复合赋值运算符

用户定义类型无法重载条件逻辑运算符 `&&` 和 `||`

# 位运算符和移位运算符

以下运算符使用整数类型或 char 类型的操作数执行位运算或移位运算

* 一元 `~（按位求补）`运算符
* 二进制 `<<（向左移位）`和 `>>（向右移位）`移位运算符
* 二进制 `&（逻辑 AND）`、`|（逻辑 OR）`和 `^（逻辑异或）`运算符

## 按位求补运算符 ~

`~` 运算符通过反转每个位产生其操作数的按位求补

```C#
uint a = 0b_0000_1111_0000_1111_0000_1111_0000_1100;
uint b = ~a;
Console.WriteLine(Convert.ToString(b, toBase: 2));
// Output:
// 11110000111100001111000011110011
```

## 左移位运算符 <<

`<<` 运算符将其左侧操作数向左移动右侧操作数定义的位数

左移运算会放弃超出结果类型范围的高阶位，并将低阶空位位置设置为零

```C#
uint x = 0b_1100_1001_0000_0000_0000_0000_0001_0001;
Console.WriteLine($"Before: {Convert.ToString(x, toBase: 2)}");
uint y = x << 4;
Console.WriteLine($"After:  {Convert.ToString(y, toBase: 2)}");
// Output:
// Before: 11001001000000000000000000010001
// After:  10010000000000000000000100010000
```
*此运算的结果始终包含至少 32 位,如果左侧操作数是其他整数类型,则其值将转换为 `int` 类型*

## 右移位运算符 >>

`>>` 运算符将其左侧操作数向右移动右侧操作数定义的位数

```C#
uint x = 0b_1001;
Console.WriteLine($"Before: {Convert.ToString(x, toBase: 2), 4}");
uint y = x >> 2;
Console.WriteLine($"After:  {Convert.ToString(y, toBase: 2), 4}");
// Output:
// Before: 1001
// After:    10
```

高顺序空位位置是根据左侧操作数类型设置的

如果左侧操作数的类型是 `int` 或 `long`  
如果左侧操作数为非负，高顺序空位位置设置为零，如果为负，则将该位置设置为 1

如果左侧操作数的类型是 `uint` 或 `ulong`  
高顺序空位位置始终设置为零

## 逻辑 AND 运算符 &

`^` 运算符计算其整型操作数的位逻辑异或，也称为位逻辑 XOR

```C#
uint a = 0b_1111_1000;
uint b = 0b_0001_1100;
uint c = a ^ b;
Console.WriteLine(Convert.ToString(c, toBase: 2));
// Output:
// 11100100
```

有一个小的交换算法
```C#
int a = 0b_1111_1000;
int b = 0b_0001_1100;
//248,28
a=a^b;
b=a^b;
a=a^b;
//28,248
```

## 逻辑或运算符 |

`|` 运算符计算其整型操作数的位逻辑 OR

```C#
uint a = 0b_1010_0000;
uint b = 0b_1001_0001;
uint c = a | b;
Console.WriteLine(Convert.ToString(c, toBase: 2));
// Output:
// 10110001
```

## 复合赋值

老规矩,支持`x op= y`,同`x = x op y`

## 运算符优先级

* 按位求补运算符 `~`
* 移位运算符 `<<` 和 `>>`
* 逻辑与运算符 `&`
* 逻辑异或运算符 `^`
* 逻辑或运算符 `|`

# 移位运算符的移位计数

对于 `x << count` 和 `x >> count` 表达式，实际移位计数取决于 `x` 的类型

* 如果 `x` 的类型为 `int` 或 `uint`，则移位计数由右侧操作数的低阶五位定义。 也就是说，移位计数通过 `count & 0x1F`（或 `count & 0b_1_1111`）计算得出。
* 如果 `x` 的类型为 `long` 或 `ulong`，则移位计数由右侧操作数的低阶六位定义。 也就是说，移位计数通过 `count & 0x3F`（或 `count & 0b_11_1111`）计算得出。

```C#
int count1 = 0b_0000_0001;
int count2 = 0b_1110_0001;
int a = 0b_0001;
Console.WriteLine($"{a} << {count1} is {a << count1}; {a} << {count2} is {a << count2}");
// Output:
// 1 << 1 is 2; 1 << 225 is 2
int b = 0b_0100;
Console.WriteLine($"{b} >> {count1} is {b >> count1}; {b} >> {count2} is {b >> count2}");
// Output:
// 4 >> 1 is 2; 4 >> 225 is 2
```
*如前例所示，即使右侧操作符的值大于左侧操作符中的位数，移位运算的结果也不可为零*

## 枚举逻辑运算符

所有枚举类型还支持 `~`、`&`、`|` 和 `^` 运算符  
对于相同枚举类型的操作数，对底层整数类型的相应值执行逻辑运算

对于具有底层类型 `U` 的枚举类型 `T` 的任何`x` 和 `y`, `x & y` 表达式生成与 `(T)((U)x & (U)y)` 表达式相同的结果

## 运算符可重载性

用户定义的类型可以重载`~`、`<<`、`>>`、`&`、`|` 和 `^` 运算符。 重载二元运算符时，对应的复合赋值运算符也会隐式重载。 用户定义类型不能显式重载复合赋值运算符

# 相等运算符

`==（相等）` 和 `!=（不等）` 运算符检查其操作数是否相等

## 相等运算符 ==

如果操作数相等，等于运算符 `==` 返回 `true`，否则返回 `false`

### 值类型的相等性

如果内置值类型的值相等，则其操作数相等

对于 `==`、`<`、`>`、`<=` 和 `>=` 运算符，如果任何操作数不是数字（`Double.NaN` 或 `Single.NaN`），则运算的结果为 `false`

这意味着 `NaN` 值不大于、小于或等于任何其他 `double`（或 `float`）值，包括 `NaN`

用户定义的 `struct` 类型默认情况下不支持 `==` 运算符。 要支持 `==` 运算符，用户定义的结构必须重载它

从 `C# 7.3` 开始，`==` 和 `!=` 运算符由 `C#` 元组支持

### 引用类型的相等性

默认情况下，如果两个非记录引用类型操作符引用同一对象，则这两个操作符相等

默认情况下，用户定义的引用类型支持 `==` 运算符。 但是，引用类型可重载 `==` 运算符。 如果引用类型重载 `==` 运算符，使用 `Object.ReferenceEquals` 方法来检查该类型的两个引用是否引用同一对象

### 记录类型相等性

在 `C# 9.0` 和更高版本中提供，记录类型支持 `==` 和 `!=` 运算符，这些运算符默认提供值相等性语义

也就是说，当两个记录操作数均为 `null` 或所有字段的对应值和自动实现的属性相等时，两个记录操作数都相等

### 字符串相等性

如果两个字符串均为 `null` 或者两个字符串实例具有相等长度且在每个字符位置有相同字符，则这两个字符串操作数相等

```C#
string s1 = "hello!";
string s2 = "HeLLo!";
Console.WriteLine(s1 == s2.ToLower());  // output: True
string s3 = "Hello!";
Console.WriteLine(s1 == s3);  // output: False
```

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
