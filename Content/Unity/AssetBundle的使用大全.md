---
title: AssetBundle的使用大全
date: 2023-04-10 22:04:00
updated: 2023-04-10 22:04:00
id: ml-20230410-220400-g184
categories:
	- Unity
	- AssetBundle
tags: 
	- Unity
	- AssetBundle
---

![AssetBundle学习路线图]

<!--more-->

# Direct References(直接引用-挂链接)

继承自`MonoBehaviour`实际上也继承自`Component`  
所以他们是组件, 脚本中的值可在 `Inspector` 中编辑

如果变量名称符合一组规则之一,则检查器显示变量名称的方式可能与您在脚本中定义变量名称的方式不同:

* 从开头删除`m_`
* 从开头删除`k`
* 从开头删除`_`
* 将第一个字母大写
* 在小写和大写字符之间添加空格
* 在下一个单词的开头的首字母缩略词和大写字符之间添加一个空格
> 在某些特殊情况下,例如`iPad`或`x64`,这些规则不适用.

这些可在 `Inspector` 中编辑的值, 也成为`Direct References`(直接引用)

> 扩展阅读`ScriptableObjects`, 独立于`MonoBehaviour`的可配置数据

# Resources Folders(Resources文件夹)

Unity定义的特殊文件夹  
资源放在一个名为 `Resources` 的文件夹中.通过使用 `Resources.Load` 函数即可加载这些资源

可在 `Assets` 文件夹中的任何位置添加多个 `Resources` 文件夹  
将所需的资源文件放在 `Resources` 文件夹内或其中的子文件夹内  
`Resources.Load` 函数的路径中包含子文件夹路径  

> 注意:如果 Resources 文件夹是 Editor 的子文件夹，则其中的资源可通过 Editor 脚本加载, 但会从构建中删除.

# Asynchronous Loading From Resources(从Resources文件夹异步加载)

`Resources.LoadAsync` 异步加载

# Asset Bundles

Unity用于资源热更新的解决方案, 将资源编入不同的AssetBundle包中, 以便后续从网络中获取并使用他们

AssetBundle是一个资源压缩包，包含模型、贴图、预制体、声音甚至整个场景，可以在游戏运行时被加载

指定资源的AssetBundle属性

首先需要设置要生成AssetBundle包的资源名称和后缀名，这个名称跟后缀名没有特定的规则，单击要生成AssetBundle包的资源，然后在Inspector视图的最下面，就可以看到设置名称和后缀名的地方

# Bundle Dependency Loading(依赖项加载)

AssetBundle自身保存着互相的依赖关系

一个Asset Bundles包可能依赖另一个Asset Bundles包, 需要先加载更底层的

例如`玩家.ab`可能依赖`玩家图集.ab`

# AssetBundle Layout Strategy(资源组合打包策略)

生成AssetBundle包时不能将所有的资源都生成到一个包中，因为这样会导致AssetBundle包的体积过大，造成下载 加载过慢 会影响用户体验，也不能为每个资源生成一个包，这样会耗费大量的加载时间，也会影响用户体验

* 按照逻辑实体分组
  * 一个UI界面或者所有UI界面为一个包
  * 一个角色或者所有角色为一个包
  * 所有场景共享的部分为一个包
* 按照类型分组(不推荐, 加载一个模型和其依赖就会加载大量整包)
  * 所有声音资源为一个包
  * 所有Shader为一个包
  * 所有模型为一个包
* 按照使用分组
  * 某一个时间内使用的所有资源打包成一个包
  * 一个关卡需要的所有资源 包括角色 贴图 声音等为一个包
  * 一个场景需要的资源为一个包
* 按照更新分组
  * 把经常更新的资源放在一个单独包内 与不经常更新的包分离

实际工作中往往是使用工具进行自动依赖分析, 依据依赖关系打包  
即如果一个模型依赖一张贴图, 且他们与其他资源没有依赖关系, 打成一个包  
一般有一个出发点, 比如某个文件夹, 通常是场景, 然后以树形结构查找保存依赖关系, 单独树枝可以单独打包.

# Bundle Manifest(Manifest文件清单)

对于生成的每个捆绑包（包括附加的清单捆绑包），都会生成关联的清单文件。清单文件可以使用任何文本编辑器打开，并包含诸如循环冗余校验 (CRC) 数据和捆绑包的依赖性数据之类的信息。对于普通 AssetBundle，它们的清单文件将如下所示
```
ManifestFileVersion: 0
CRC: 2422268106
Hashes:
  AssetFileHash:
    serializedVersion: 2
    Hash: 8b6db55a2344f068cf8a9be0a662ba15
  TypeTreeHash:
    serializedVersion: 2
    Hash: 37ad974993dbaa77485dd2a0c38f347a
HashAppended: 0
ClassTypes:
- Class: 91
  Script: {instanceID: 0}
Assets:
  Asset_0: Assets/Mecanim/StateMachine.controller
Dependencies: {}
```

这些还有一个统计清单的清单, 如下
```
ManifestFileVersion: 0
AssetBundleManifest:
  AssetBundleInfos:
    Info_0:
      Name: scene1assetbundle
      Dependencies: {}
```
这将显示 AssetBundle 之间的关系以及它们的依赖项  
这对于确定在运行时加载哪些捆绑包依赖项非常有用

参考以下示例，`ModeBundle 1` 中的材质引用了 `ModeBundle 2` 中的纹理

很显然, 我们应该依据`Manifest`清单首先加载更底层的`ModeBundle 2`

# Duplicate vs.shared Assets(共享资源管理)


默认情况下，Unity 不会优化 AssetBundle 之间的重复信息。这意味着您项目中的多个 AssetBundle 可能包含相同信息，例如多个预制件使用的同一种材质

参考以下示例，`ModeBundle 1` 含有材质`Mat1`, `ModeBundle 2` 中也可以包含材质`Mat1`. 这取决于你的打包策略

很显然, 我们应该减少重复的资源占用, 更好的方是是使`ModeBundle 1`,`ModeBundle 2`都依赖于包含`Mat1`的`MatBundle 1And2`. 并使他们自身不包含这个材质

如此, 我们有了一个共享资源的概念

> 注意, 如果没有实现加载依赖的材质包, 就会出现材质丢失的粉色

# Memory Management(内存管理)

AssetBundle.Unload(true) 卸载从 AssetBundle 加载的所有游戏对象（及其依赖项）

如果最终会将所有的AssetBundle都加载进来, 内存会非常大.  
有意需要有一些策略来移除一些不需要的包

如果卸载了其他运行时资源仍在依赖包, 则无法在找到这些依赖.  
例如在`ModeBundle 1`还在场景中时, `MatBundle 1And2`, 将会出现材质丢失的粉色

两种常用方法是：

1. 在应用程序生命周期中具有明确定义的卸载瞬态 AssetBundle 的时间点，例如在关卡之间或在加载屏幕期间。

2. 维护单个对象的引用计数，仅当未使用所有组成对象时才卸载 AssetBundle。这允许应用程序卸载和重新加载单个对象.

# ShaderVariantsCollect(shader变体收集)

在写shader时，往往会在shader中定义多个宏，并在shader代码中控制开启宏或关闭宏时物体的渲染过程。最终编译的时候也是根据这些不同的宏来编译生成多种组合形式的shader源码。其中每一种组合就是这个shader的一个变体(Variant)

Material所包含的Shader Keywords表示启用shader中对应的宏，Unity会调用当前宏组合所对应的变体来为Material进行渲染  

在Editor下，可以通过将material的inspector调成Debug模式来查看当前material定义的Keywords，也可在此模式下直接定义Keywords，用空格分隔Keyword

multi_compile会默认生成所有的变体，因此应当谨慎适用multi_compile，否则将会导致变体数量激增

如` #pragma multi_compile A B C #pragma multi_compile D E`  
则此时会生成 `A D、A E、B D、B E、C D、C E`这6中变体

项目中shader变体的生成方式主要有三种

1. shader与material打在一个包中(不推荐)
   1. 变体根据material中的keywords自动生成
   2. 多个不同的material包中可能存在相同的shader变体，造成资源冗余
   3. 若在程序运行时动态改变material的keyword，使用shader_feature定义的宏，其变体可能并没有被生成
2. Shader单独打包，使用multi_compile定义全部宏(不推荐)
   1. 全部变体都被生成，不会发生需要的变体未生成的情况
   2. 生成的变体数量庞大，严重浪费资源
3. Shader单独打包，shader_feature（需要使用ShaderVariantCollection生成变体）
   1. 能够有效控制shader_feature变体数量
   2. 需要程序定义变体收集规则
      1. 如何确定哪些变体需要生成
      2. 容易遗漏需要生成的变体，特别是需要动态替换的变体

## 使用shader_feature的解决方案：ShaderVariantCollection

Shader Variant Collection的其中一个作用就是用来记录shader中使用shader_feature定义的宏产生的变体。能够设置生成何种变体，从而避免生成不必要的变体；shader不必和material打在一个包中，避免了多个包中存在相同的变体资源；明确直观的显示了哪些变体是需要生成的

在Unity中可以通过Create->Shader-> Shader Variant Collection，就可以新建一个shader variant collection文件

配置好需要生成的变体后，将collection与shader打在同一个包中，便能准确生成面板中所配置的shader变体

> 注意: 必定生成首个宏定义开启所对应的变体

当collection将变体准确生成后，便能在运行时通过修改material中的keywords来实现对不同变体的调用

> 在使用ShaderVariantCollection收集变体打包时，只对shader_feature定义的宏有意义，multi_compile的变体不用收集也会被全部打进包体

> 变体可以预热, 加载更流畅

### Shader中有多个Pass时变体的生成规则

1. 读取ShaderVariantCollection中已存在的变体，获取它们的Keywords
2. 将这些Keywords分别与每个Pass的多组Keywords列表求交集，取交集中Keywords数量最多得那组
3. 用得到的Keywords与对应的PassType生成ShaderVariant，并添加到ShaderVariantCollection中
4. 若得到得交集中有新的Keywords，则回到b

形如
```
ABC ∩ Add AE -> Add A (A is NewKeyword)
  A ∩ Base ABC -> Base A
  A ∩ Normal ABE -> Normal A
ABC ∩ Normal ABE -> Normal AB (AB is NewKeyword)
 AB ∩ Base ABC -> Base AB
 AE ∩ Normal ABE -> Normal AE
```

## shader_feature方案项目具体实现方式

* 在`ProjectSetting > Graphics`的最下面，先Clear掉当前的记录，然后进行游戏，尽量覆盖大多数游戏内容后，之后点击`Save to asset`保存  
  * 容易漏变体
  * 不好更新
* 自定义工具, 即自定义收集规则
  * 确定哪些材质会被打包
  * 确定这个材质会引用哪个、哪些变体
  * 将变体写入变体收集文件
  * 代码修改的变体无法收集
    * 这个需要视项目而定, 最好能有一个修改表
* 或通过`IPreprocessShaders`接口来重写剔除方法实现自己想要的变体剔除
  * 内建keyword可能被引擎的一些设置功能先行剔除掉，在IPreprocessShaders接口传入时已经不存在，需要通过修改引擎设置解除剔除


参考代码-变体收集修改
```CSharp
//将SerializedProperty转化为ShaderVariant
private ShaderVariantCollection.ShaderVariant PropToVariantObject(Shader shader, SerializedProperty variantInfo)
{
    PassType passType = (PassType)variantInfo.FindPropertyRelative("passType").intValue;
    string keywords = variantInfo.FindPropertyRelative("keywords").stringValue;
    string[] keywordSet = keywords.Split(' ');
    keywordSet = (keywordSet.Length == 1 && keywordSet[0] == "") ? new string[0] : keywordSet;
    
    ShaderVariantCollection.ShaderVariant newVariant = new ShaderVariantCollection.ShaderVariant()
    {
        shader = shader,
        keywords = keywordSet,
        passType = passType
    };

    return newVariant;
}

//将ShaderVariantCollection转化为Dictionary用来访问
private void ReadFromFile()
{
    mMapper.Clear();
    
    SerializedObject serializedObject = new UnityEditor.SerializedObject(mCollection);
    //serializedObject.Update();
    SerializedProperty m_Shaders = serializedObject.FindProperty("m_Shaders");
    
    for (int i = 0; i < m_Shaders.arraySize; ++i)
    {
        SerializedProperty pair = m_Shaders.GetArrayElementAtIndex(i);

        SerializedProperty first = pair.FindPropertyRelative("first");
        SerializedProperty second = pair.FindPropertyRelative("second");//ShaderInfo

        Shader shader = first.objectReferenceValue as Shader;

        if (shader == null)
            continue;
            
        mMapper[shader] = new List<SerializableShaderVariant>();

        SerializedProperty variants = second.FindPropertyRelative("variants");
        for (var vi = 0; vi < variants.arraySize; ++vi)
        {
            SerializedProperty variantInfo = variants.GetArrayElementAtIndex(vi);

            ShaderVariantCollection.ShaderVariant variant = PropToVariantObject(shader, variantInfo);
            mMapper[shader].Add(new SerializableShaderVariant(variant));
        }
    }
}
```

```CSharp
public static void DoCollect(bool increment = true) 
{
	if (increment)
	{
		CreateDir(ShaderVariantPath);
	}
	else
	{
		ClearShaderVariant();
	}
	
	// Dictionary<Sahder名字,List<Material>> 收集所有材质球
	Dictionary<string,List<Material>> matDic = FindAllMaterials(ResPath);
	shaderNumber = matDic.Count;

	// 用来收集所有关键词  
	Dictionary<string, Dictionary<string, Material>> finalMats = new Dictionary<string, Dictionary<string, Material>>();
	
	List<string> temp = new List<string>();
	
	int idx = 0; //进度条
	
	// 遍历所有的材质球字典
	foreach (var item in matDic)
	{
		#region 通过matDic中获取的ShaderName来判断finalMats中是否存 在来创建
		string shaderName = item.Key;
		EditorUtility.DisplayProgressBar($"收集材质 {shaderName}", "收集关键字", (float)idx++ / matDic.Count);        // 显示或更新进度条
		
		// 判断finalMats里Value是否包含shaderName
		if (finalMats.TryGetValue(shaderName, out Dictionary<string,Material> matList) == false)
		{
			matList = new Dictionary<string, Material>();

			finalMats.Add(shaderName, matList);
		}
		#endregion

		
		//填充 matList
		foreach (var mat in item.Value)
		{
			matNumber++;
			
			temp.Clear();
			string[] keyWords = mat.shaderKeywords;                   // 只会收集定义了的变体名字
			// foreach (var key in keyWords)
			// {
			//     Debug.Log(key);
			// }
			temp.AddRange(keyWords);                                  // 变体名字添加到temp中
			// 获取和设置是否为此材质启用 GPU 实例化
			if (mat.enableInstancing)
			{
				// temp添加enableInstancing
				temp.Add("enableInstancing");
			}

			if (temp.Count == 0)
			{
				continue;                                            // 如果变体关键字数为0 跳出当前的循环，强迫开始下一次循环
			}


			temp.Sort();
			// foreach (var t in temp)
			// {
			//     Debug.Log(t);
			// }
			
			string pattern = string.Join("_", temp);        // 把单个材质的变体关键字组合在一起
			//Debug.Log(pattern);
			
			// 如果 matList 中 不包含 pattern
			if (!matList.ContainsKey(pattern))
			{
				matList.Add(pattern, mat);                         // 变体关键字组合对应的材质球
			}
		}
	}
	
	idx = 0;
		//     ----    从这里开始将着色器变体添加到变体集
	foreach (var kv in finalMats)
	{
		string shaderFullName = kv.Key;                             // shader的名字
		//Debug.Log(shaderFullName);
		Dictionary<string,Material> matList = kv.Value;             // 变体关键字组合对应的材质球

		if (matList.Count == 0)
		{
			continue;
		}

		EditorUtility.DisplayProgressBar($"收集材质 {shaderFullName}", "一般材质变体", (float)idx++ / finalMats.Count);        // 显示或更新进度条
		
		// 跳过遗留的shader
		if (shaderFullName.Contains("InternalErrorShader"))
		{
			continue;
		}

		
		// 变体集存放路径+shaderName.shadervariants
		var path = $"{ShaderVariantPath}/{shaderFullName.Replace("/", "_")}.shadervariants";
		//Debug.Log(path);
		bool alreadyExsit = true; 
			
		ShaderVariantCollection shaderCollection = AssetDatabase.LoadAssetAtPath<ShaderVariantCollection>(path);   // 加载SVC
		
		if (shaderCollection == null)
		{
			alreadyExsit = false;
			shaderCollection = new ShaderVariantCollection();                                                     // 实例化SVC
		}

		Shader shader = Shader.Find(shaderFullName);
		foreach (var kv2 in matList)
		{
			ShaderVariantData svd = GetShaderVariantEntriesFiltered(shader, kv2.Value.shaderKeywords);            // 获得着色器变体条目过滤
			// 遍历Shader的pass类型
			foreach (var passType in svd.passTypes)
			{
				// 创建shader变体，在构造参数填入shader，pass类型，以及关键字
				ShaderVariantCollection.ShaderVariant shaderVariant = new ShaderVariantCollection.ShaderVariant()
				{
					shader = shader,
					passType = (UnityEngine.Rendering.PassType)passType,
					keywords = kv2.Value.shaderKeywords,
				};
				// 如果变体集不包含改变体则添加进去
				if (!shaderCollection.Contains(shaderVariant))
				{
					shaderCollection.Add(shaderVariant);            // Shader变体添加到变体集
				}
			}
		}
		
		if (alreadyExsit)
		{
			EditorUtility.SetDirty(shaderCollection);                             // 表示已经创建
		}
		else
		{
			AssetDatabase.CreateAsset(shaderCollection, path);                   // 创建SVC资产
			svcNumber++;
		}
	}
	
	// save
	EditorUtility.DisplayProgressBar("收集材质", "保存资产", 1f);        // 显示或更新进度条
	AssetDatabase.Refresh();                                                         // 刷新导入所有更改的资源
	AssetDatabase.SaveAssets();                                                      // 将所有未保存的资源更改写入磁盘
	EditorUtility.ClearProgressBar();                                                // 移除进度条

	//Debug.Log("完成所有shader变量的收集!");
	
}
```

# ~~Addressable(可寻址资源)~~

待续

# 完毕

**感谢您的观看!**  
本文来自 [ML-Blog][ML-Blog_Link]

<!-- 图片 -->
[AssetBundle学习路线图]:https://github.com/UserMingHaoLi/ML_HexoBlogContentImages/blob/main/Content/AssetBundle%E4%BD%BF%E7%94%A8%E5%A4%A7%E5%85%A8/AssetBundle%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF.png?raw=true `AssetBundle学习路线图`
<!-- 链接 -->

<!-- 水印 -->
[ML-Blog_Link]:https://userminghaoli.github.io/ `我的博客`
