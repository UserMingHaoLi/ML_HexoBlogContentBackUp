---
title: CSharp类解析_Path
date: 2023-05-05 23:53:00
updated: 2023-05-05 23:53:00
id: ml-20230505-235300-g185
categories:
	- 基础
	- CSharp类详解
tags: 
	- 基础
	- CSharp类详解
	- CSharp
---

`System.IO.Path`   


<!--more-->

官方定义: 对包含文件或目录路径信息的 String 实例执行操作。 这些操作是以跨平台的方式执行的

路径的确切格式由当前平台确定。 例如，在某些系统上，路径可以从驱动器或卷号开始，而此元素在其他系统中不存在。 在某些系统上，文件路径可以包含扩展，这些扩展指示存储在文件中的信息类型。 文件扩展名的格式依赖于平台;例如，某些系统将扩展限制为三个字符，而其他系统则不这样做。 当前平台还确定用于分隔路径元素的字符集，以及指定路径时无法使用的字符集。 由于这些差异， `Path` 的确切行为依赖于平台

`Path` 大多数成员不会与文件系统交互，并且不验证路径字符串指定的文件是否存在

使用接受路径字符串的方法时，请确保路径格式良好, 可能引发异常

对于`File://`, `HTTP://` 这种特殊路径, 推荐查看源码或构建测试用例

---

```CSharp
//一些跨平台符号定义
//斜杠
#if !PLATFORM_UNIX        
        public static readonly char DirectorySeparatorChar = '\\';
#else
        public static readonly char DirectorySeparatorChar = '/';
#endif 
#if !PLATFORM_UNIX        
        public static readonly char AltDirectorySeparatorChar = '/';
#else
        public static readonly char AltDirectorySeparatorChar = '\\';
#endif 
//平台特定的卷分隔符
#if !PLATFORM_UNIX
        public static readonly char VolumeSeparatorChar = ':';
#else
        public static readonly char VolumeSeparatorChar = '/';
#endif
//用于在环境变量中分隔路径字符串的平台特定的分隔符
#if !PLATFORM_UNIX
        public static readonly char PathSeparator = ';';
#else
        public static readonly char PathSeparator = ':';
#endif 
//一些不允许的字符, 过时是因为将其分割为了RealInvalidPathChars和InvalidFileNameChars
[Obsolete("Please use GetInvalidPathChars or GetInvalidFileNameChars instead.")]
public static readonly char[] InvalidPathChars = { '\"', '<', '>', '|', '\0', (Char)1, (Char)2, (Char)3, (Char)4, (Char)5, (Char)6, (Char)7, (Char)8, (Char)9, (Char)10, (Char)11, (Char)12, (Char)13, (Char)14, (Char)15, (Char)16, (Char)17, (Char)18, (Char)19, (Char)20, (Char)21, (Char)22, (Char)23, (Char)24, (Char)25, (Char)26, (Char)27, (Char)28, (Char)29, (Char)30, (Char)31 };
internal static readonly char[] TrimEndChars = { (char) 0x9, (char) 0xA, (char) 0xB, (char) 0xC, (char) 0xD, (char) 0x20,   (char) 0x85, (char) 0xA0};
private static readonly char[] RealInvalidPathChars = { '\"', '<', '>', '|', '\0', (Char)1, (Char)2, (Char)3, (Char)4, (Char)5, (Char)6, (Char)7, (Char)8, (Char)9, (Char)10, (Char)11, (Char)12, (Char)13, (Char)14, (Char)15, (Char)16, (Char)17, (Char)18, (Char)19, (Char)20, (Char)21, (Char)22, (Char)23, (Char)24, (Char)25, (Char)26, (Char)27, (Char)28, (Char)29, (Char)30, (Char)31 };
//区别是':', '*', '?', '\\', '/'
private static readonly char[] InvalidFileNameChars = { '\"', '<', '>', '|', '\0', (Char)1, (Char)2, (Char)3, (Char)4, (Char)5, (Char)6, (Char)7, (Char)8, (Char)9, (Char)10, (Char)11, (Char)12, (Char)13, (Char)14, (Char)15, (Char)16, (Char)17, (Char)18, (Char)19, (Char)20, (Char)21, (Char)22, (Char)23, (Char)24, (Char)25, (Char)26, (Char)27, (Char)28, (Char)29, (Char)30, (Char)31, ':', '*', '?', '\\', '/' };
//一些长度定义
internal static readonly int MaxPath = 260;
private static readonly int MaxDirectoryLength = 255;
internal const int MAX_PATH = 260;  // From WinDef.h
internal const int MAX_DIRECTORY_PATH = 248; 
//获取倒数第一个`.`s = path.Substring(0, i);  extension最好以`.`开头,不然会`s = s + "."`; 最后s = s + extension;
public static String ChangeExtension(String path, String extension);
//传入file路径, 获取倒数第一个斜杠的路径(传入Dir路径也可返回标准化路径)
//传入根目录将返回null
public static String GetDirectoryName(String path);
//获取根路径的长度, 一般就在1-3之间, 做了硬代码优化
internal static int GetRootLength(String path);
//公开RealInvalidPathChars的Clone
public static char[] GetInvalidPathChars();
//公开InvalidFileNameChars的Clone
public static char[] GetInvalidFileNameChars();
//获取倒数第一个`.`s = path.Substring(0, i); 
public static String GetExtension(String path);
//标准化路径后, 使用FileIOPermission获取其路径信息权限, 标准化路径会使用win32api获取FullPath
//注意, 此方法访问文件系统, 且会获取权限(路径信息访问权限,不包含读写)
public static String GetFullPath(String path);
//不安全的, 因为不提前申请权限, 直接访问
internal static String UnsafeGetFullPath(String path);
//标准化路径, 对尾部非法字符剔除 ,检测字符非法(相对路径, 路径起始), 对可标准化的作标准化
//注意其中有分配栈上内存char*[260]
internal unsafe static String NormalizePath(String path, bool fullCheck, int maxPathLength, bool expandShortPaths);
//是否包含@"\\?\"
internal unsafe static bool HasLongPathPrefix(String path);
//从path前添加@"\\?\"
internal unsafe static String AddLongPathPrefix(String path);
//消除起始的@"\\?\"
internal unsafe static String RemoveLongPathPrefix(String path);
internal unsafe static StringBuilder RemoveLongPathPrefix(StringBuilder path);
//获取倒数第一个斜杠, path.Substring(i + 1, length - i - 1);
public static String GetFileName(String path);
//GetFileName, 然后查找`.`, path.Substring(0,i);
public static String GetFileNameWithoutExtension(String path);
//path.Substring(0, GetRootLength(path));
public static String GetPathRoot(String path);
//Win32API发现用户临时文件夹路径
public static String GetTempPath();
//是否是相对路径, 原理是检测是否从盘符或根路径起始
internal static bool IsRelative(string path);
//获取随机文件名, 默认文件名8位, 扩展名3位, 加.共计12位
//调用ToBase32StringSuitableForDirName
public static String GetRandomFileName();
//GetTempPath目录上创建一个唯一命名的0字节临时文件, 并返回该文件的完整路径
//访问文件系统, 并获取写入权限
public static String GetTempFileName();
internal static String UnsafeGetTempFileName();
private static String InternalGetTempFileName(bool checkHost) ;
//是否有扩展名, 即倒数检测`.`
public static bool HasExtension(String path);
//是否Root路径, 即检测前两个字符是否斜杠或`:`
public static bool IsPathRooted(String path);
//按顺序两两拼接, 有Root路径, 仅保留Root, 然后继续拼接
//拼接使用 path1 + DirectorySeparatorChar + path2, 如果自带斜杠或`:`, 则path1 + path2;
//任何后续参数是用户输入的字符串，请改为调用 Join 或 TryJoin 方法。
public static String Combine(String path1, String path2);
public static String Combine(String path1, String path2, String path3);
public static String Combine(String path1, String path2, String path3, String path4);
public static String Combine(params String[] paths);
private static String CombineNoChecks(String path1, String path2);
//随机范围是小写a-z加0-5, 共计32个, 本质还是通过Key+算法生成固定数据
internal static String ToBase32StringSuitableForDirName(byte[] buff);
//如使用..上移目录, 抛出异常
internal static void CheckSearchPattern(String searchPattern);
//检查每个字符, 其中不得出现 " < > | 和ASCII小于32的内容, 如checkAdditional,还不得出现 ? *
//如果出现非法字符, 返回true表示检测到(不负责抛出异常)
internal static bool HasIllegalCharacters(String path, bool checkAdditional);
//调用HasIllegalCharacters, 检测非法字符, 抛出异常
internal static void CheckInvalidPathChars(String path, bool checkAdditional = false);
//内部使用的Combine, 少些检测, 性能稍好一点
internal static String InternalCombine(String path1, String path2)
```


# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->

<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ "我的博客"
