众所不周知，KCES 全称 KissCharacter EditSystem ，是一个独立的编辑系统，其设计目的是为了在 KISS 的多部作品之间共享3D资产，而不需要每个作品都重新创建3D资产。

KCES 分为 1 代和 2 代，1 代版本号为 1.34.5，而 2 代的起始版本号是 1.35.0 可见其实就是小幅度升级，想再卖一次而已。
（甚至你购买 KCES2 了会给你一个安装包和一个升级包，使用安装包会告诉你装了 KCES1 所以不能装 KCES2，只能升级）

KCES2 可以用在 COM3D2.5 和将来的新作 [CRC3D3](https://crc3d3.jp) 上，所以我们直接给 KCES2 做 MOD，就能同时在 2 个游戏上使用了，岂不美哉？

KCES1 不支持 CRC3D3 所以也就没有什么存在的必要了，所有内容没说就是以 KCES2 为准。

<br>

首先旧有的 COM3D2 MOD 肯定是不能在 KCES 上使用了，虽然根基是一样的，但是 KISS 进行了大量修改。

由于根基是一样的，请确保你已经知道如何给 COM3D2 制作 MOD，部分细节不再赘述，请见本人的 90135 的 COM3D2 简明 MOD 教程。

# 游戏如何读取 MOD

## 两条路

在 COM3D2 MOD 制作里面我说过，COM3D2 直接读取 MOD 文件夹内的 .menu，.mate，.model 这些能力是官方提供的功能。

然而 KCES 还没有提供这种功能，大和P（游戏制作人）说还在考虑中。

### HairMaker

KCES2 中加入了 HairMaker 功能，用于分享玩家自制的发型，引入了 .kcmenu, .kcmat, .kcmodel, .kcmesh 等文件。

对应 COM3D2 的 .menu，.mate，.model 文件。

目前还只支持做头发，说不定之后会支持我们做其他的东西，但是目前此路不通。

<details>

<summary>细节，不用阅读</summary>

KCES2 有个"自制头发"（HairMake）功能，做完能导出分享。它的导入导出走的是一套**完全独立于 AssetBundle 的散文件通道**，而这套通道有可能被拿来做普通 MOD。

### 目录位置

```
<存档目录>/hair_make/<任意名字>/
```

`<存档目录>` 由 `SerializeLocation` 决定：

| `serialize_storage_config.cfg` 里的 `locationTypeText` | 存档目录                      |
|--------------------------------------------------------|-------------------------------|
| 不存在 / `Normal`（默认）                              | `<游戏根目录>/SaveData/`      |
| `Document`                                             | `我的文档/KISS/CRC/SaveData/` |

这个目录被注册成一个 `FileSystemWindowsDirect`，检索时用的是 `SearchOption.AllDirectories`，**所以子目录可以任意套多深**。

### 文件组成

| 扩展名     | 内容                                                                                 | 对应 COM3D2 的      |
|------------|--------------------------------------------------------------------------------------|---------------------|
| `.kcmenu`  | 单个 `Parts.Menu`（LZ4 MessagePack）                                                 | `.menu`             |
| `.kcmodel` | 单个 `Parts.Model`（骨骼 / 材质引用 / 形态键，**不含网格**）                         | `.model` 的前半部分 |
| `.kcmesh`  | 网格本体（34 槽未压缩 MessagePack，顶点/法线/切线/最多 8 组 UV/BlendShape/bindpose） | `.model` 的后半部分 |
| `.kcmat`   | 单个 `Parts.Material`                                                                | `.mate`             |
| `.kctex`   | `CM3D2_TEX` version 1000，载荷是 **PNG**                                             | `.tex`              |
| `.kcmmeta` | 元数据（指向 model / 原始菜单 / GUID / 版本）                                        | 无对应物            |

### 文件名规则

导出器（`ExportKCES`）的命名是这样的，都不带原格式的扩展名：

| 文件            | 名字怎么来                                                                                | 例子                                  |
|-----------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| 目录            | `hair_make/<导出名>/`，导出名自动生成为 `<年>_<月日>_<时分>_<源菜单名>`，重名加 `_0` `_1` | `hair_make/2026_0829_1430_hairf_001/` |
| `.kcmodel`      | `<模型名>.kcmodel`，模型名会被强制小写                                                    | `myhair.kcmodel`                      |
| `.kcmesh`       | `<模型名>.kcmesh`                                                                         | `myhair.kcmesh`                       |
| `.kcmmeta`      | `<模型名去掉扩展名>.kcmmeta`                                                              | `myhair.kcmmeta`                      |
| `.kcmat`        | `<Unity 材质名>.kcmat`（会去掉 ` (Instance)` 后缀）                                       | `hair_mat.kcmat`                      |
| `.kctex`        | `<材质名>_<材质序号>_<shader 属性索引>.kctex`                                             | `hair_mat_0_12.kctex`                 |
| `.kcmenu`       | `<kcmmeta 文件名去扩展名>.kcmenu`                                                         | `myhair.kcmenu`                       |
| `.harimakesave` | `<导出名>.harimakesave`，自制头发的可继续编辑存档                                         | `myhair.harimakesave`                 |

几个注意点：

- **模型名里不要带点。** `.kcmmeta` 用的是 `Path.GetFileNameWithoutExtension(model.fileName)`，名字里有点会被截断。
- **全部用小写。** `.kcmodel` 那步会强制小写，但 `.kcmesh` 用的是你传进去的原始大小写，两边不一致就对不上了。
- `.harimakesave` 这个拼写是 KISS 自己写错的（应该是 hair 而不是 hari），照抄就行。

### 加载流程

启动时 `GameMain.OnSetUpCheck()` 调一次：

```csharp
HairMakeController.LoadExportMenu();
```

它把 `hair_make` 下**所有** `.kcmenu` 反序列化成 `Menu` 并 `PartsMenuManager.RegisterMenu()` 注册为运行时资源。之后装备这个部件时：

- `.kcmodel` → `PartsModelManager.CreateModel` 认出扩展名，走 `ReadFileForHairMake`（读 `hair_make` 目录）
- `.kcmesh` → 同上，由 `model.meshFileName` 引用
- `.kcmat` → `ImportKCES.GetMaterial`，**shader 用 `Shader.Find(shaderName)` 按名字找**
- `.kctex` → `ImportKCES.CreateTexture2D`，运行时解 PNG

### 这条路线的意义和限制

**意义**：不用碰 Unity、不用打 AssetBundle、不用生成 `.ct`，把 6 种文件丢进一个文件夹就完事。对 MOD 作者来说这是理想形态。

**限制和未知**（都**未验证**）：

- 这套东西是为**头发**设计的。`.kcmenu` 的 `category`（MPN）来自被克隆的源菜单，理论上可以填任意 MPN，但游戏 UI 是否愿意在非头发分类里显示它，没验证过。
- `kcmodelMode` 下材质走 `Shader.Find(shaderName)`，和正常路线的 `Resources.Load("DefMaterial/" + shaderFileName)` 不是同一套。**shader 名必须是游戏内已存在的**，而且能用的 shader 集合可能和正常路线不同。
- `RegisterRuntimeAsset` 遇到 `id` 冲突会直接**拒绝注册并打警告**，不会覆盖官方资源。所以想靠这条路线"替换"官方部件是不行的，只能"新增"。
- 只在 KCES2 验证过有这套代码（KCES1 的 `Parts/` 目录里没有 `PartsCatalogAssetLoader`，HairMake 也是 KCES2 才有的功能）。

</details>

## 做成和游戏本体一样的文件

所以目前我们只能从游戏本身读取 DLC 的方式入手。

打开 `KCES\GameData` 文件夹里面可以看到很多 .aba 和 .ct 文件，这就是游戏本体读取资产的文件。

.aba (AssetBundleAddressable? AssetBundleArchive?) 本质上是 UnityFS 也就是 Unity AssetBundle，就是标准的 Unity 用来打包文件的东西。

.ct (catalog) 是 KISS 自创的索引文件，用来标识 aba 里面有什么，否则一但 DLC 多了，啥都要读取进去就很慢很卡。

```
<游戏根目录>/GameData/
               ├── xxx.ct         ← Content Table，索引（VirtualDirectory 格式）
               ├── xxx.aba        ← Unity AssetBundle（UnityFS），真正的资源在这里
               ├── assets/        ← 同样扫描 *.ct
               ├── dlc/           ← 同样扫描 *.ct
               └── paths.dat      ← 只用于 COM3D2/CM3D2 兼容挂载（KCES 自己不需要）
```

游戏启动时：

1. 扫 `GameData/`、`GameData/assets/`、`GameData/dlc/` 三个目录下的 `*.ct`（不扫描子文件夹）
2. 每个 `.ct` 解出一个 `AssetBundleCatalog`，里面有：
    - `items` —— 按资源名 FNV-a hash 升序排列的主索引，用于查找资源
    - `extensionList` / `ExtensionNameList` —— 按扩展名分组的二级索引，用于列出所有某种类型的文件
    - `resourceFileNames` —— 说明这个文件存在哪个 `.aba` 里面

### 打开看看有什么

为此，本人制作了一套工具

- [ABA EXPLORER](https://github.com/MeidoPromotionAssociation/ABA_EXPLORER)
- [KCES MOD EDITOR](https://github.com/MeidoPromotionAssociation/KCES_MOD_EDITOR)
- [MeidoSerialization](https://github.com/MeidoPromotionAssociation/MeidoSerialization)

ABA EXPLORER 用户解包和打包 aba 和 ct

KCES MOD EDITOR 用于编辑解包出来的所有文件

MeidoSerialization 是二者的底层库，但同时也提供命令行工具， KCES MOD EDITOR 不支持编辑的一些格式，也可以用 MeidoSerialization CLI 转成 JSON 编辑


解包一个 aba 里面可以看到除了一些 Unity 标准文件之外还有 KISS 自定义的文件，Unity 标准文件我们就不说了

![img.png](image/img.png)

## 认识文件

文件表

| 文件类型              | 全名                                                         | 说明                                                                                                             |
|-----------------------|--------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `.aba`                | AssetBundleAddressable? AssetBundleArchive?                  | 一个 Unity Assets Bundle                                                                                         |
| `.ct`                 | catalog 目录表                                               | aba 的索引表                                                                                                     |
| `.menuassets`         | Menu Assets 菜单资产                                         | 是一个 Menu 包，一个文件内可以包含 N 个 KCES 的 Menu                                                             |
| `.materialassets`     | Material Assets 材质资产                                     | 是一个 Mate 包，一个文件内可以包含 N 个 KCES 的 Mate                                                             |
| `.pmatassets`         | Priority Material Assets 材质优先级资产                      | 是一个 Pmat 包，一个文件内可以包含 N 个 KCES 的 Pmat                                                             |
| `.model`              | Model 模型                                                   | 不包含网格，只包含骨骼、形态键和材质，需要配合 `.mmesh` 使用                                                     |
| `.mmesh`              | ？ Unity 原生 Mesh                                           | 仅包含网格，需要配合 `.model` 使用                                                                               |
| `.anm`                | Unity 原生 Animation                                         | 动作                                                                                                             |
| `.tex`                | Unity 原生 Texture2D                                         | 纹理/贴图                                                                                                        |
| `.audioclip`          | Unity 原生 AudioClip                                         | 声音                                                                                                             |
| `.sprite`             | Unity 原生 Sprite                                            | 里面不含实际的像素数据，包含 rect、pivot、border、渲染数据键、渲染网格、碰撞形状                                 |
| `.partsatlas`         | Unity 原生 SpriteAtlas                                       | 里面不含实际的像素数据，包含 m_RenderDataMap，即每个 sprite 在图集中的位置                                       |
| `sactx-<*>.texture2d` | Unity 原生 Texture2D                                         | KISS 自己的文件虽然格式一样，但是是 `.tex` 后缀，这个后缀的是 Unity 自动生成的，一般是 sprite 实际像素的储存位置 |
| `.undressdat`         | Undress data 内衣扒开设置数据                                | 用于扒开内衣功能的数据，需要进行烘焙，目前无法制作                                                               |
| `.undresspdat`        | Undress pre data 内衣扒开预计算缓存                          | 用于扒开内衣功能的数据，需要进行烘焙，目前无法制作                                                               |
| `.nson`               | NSON 一种JSON                                                | 存各种文本数据                                                                                                   |
| `.nei`                | ？                                                           | 加密 CSV 文件，KCES 的换成了 UTF-8 编码，存各种文本数据                                                          |
| `.preset`             | preset 角色预设文件                                          | KCES 角色预设文件，里面的内容变了                                                                                |
| `.asset_bg`           | Background assets 背景资产                                   | 和 aba 一样的东西，只是名称不同                                                                                  |
| `.asset_scene`        | assets Scene 场景资产                                        | 和 aba 一样的东西，只是名称不同                                                                                  |
| `.dbconf`             | Dynamic Bone Config 摇曳骨配置文件                           | 基本上和老的摇曳骨差不多，但是底层换了 Magick Cloth                                                              |
| `.dsbconf`            | Dynamic Skirt Bone Config 摇曳裙骨配置文件                   |                                                                                                                  |
| `.dslconf`            | Dynamic Sleeve Bone Config 摇曳袖子骨配置文件                | 已废弃                                                                                                           |
| `.db2conf`            | Dynamic Bone 2 Config  摇曳骨2配置文件                       | 全新的摇曳骨，底层换了 Magick Cloth 2                                                                            |
| `.dsb2conf`           | Dynamic Skirt Bone 2 Config 摇曳裙骨2配置文件                |                                                                                                                  |
| `.dsl2conf`           | Dynamic Sleeve Bone 2 Config 摇曳袖子骨2配置文件             |                                                                                                                  |
| `.dbcol`              | Dynamic Bone Collider Config 摇曳骨碰撞器配置文件            |                                                                                                                  |
| `.dslcol`             | Dynamic Sleeve Bone Collider Config 摇曳袖子骨碰撞器配置文件 |                                                                                                                  |

KCES 加了一堆摇曳骨/动态骨的配置文件，看名字会觉得莫名其妙，所以我把全名标出来了，这样就很清晰了


玩家一般不可触碰之物

| 文件类型                  | 全名                                    | 说明                                                                              |
|---------------------------|-----------------------------------------|-----------------------------------------------------------------------------------|
| `.ikcol`                  | IK Collider Config IK 碰撞器配置文件    | 全局唯一的玩意                                                                    |
| `.limbcol`                | Limb Collider Config 肢体碰撞器配置文件 | 全局唯一的玩意                                                                    |
| `.maid_collider[*].bytes` | Maid Collider  女仆碰撞器数据           | VR 抓取 / 触摸判定用的身体胶囊，以及编辑模式摆放部件时的碰撞，全局唯一的玩意      |
| `<区域>.hitcheck`         | Hitcheck 碰撞检测                       | CM3D2 时代的身体碰撞球集合，只有 4 个全局文件，KCES 完全不读它                    |
| `.sad`                    | Saved Attach Data 保存的附着点数据      | 保存部件挂载关系和变换，数据交换桥接文件在 KCES 导出到 COM3D2.5 时生成            |
| `.brd`                    | Bridge 桥接文件                         | 是 KCES ↔ COM3D2.5 角色互传的载体，数据交换桥接文件在 KCES 导出到 COM3D2.5 时生成 |
| `.vd`                     | Virtual Directory 虚拟文件夹            | 数据交换桥接文件在 KCES 导出到 COM3D2.5 时生成                                    |
| `.enm`                    | ExportFile Name Map 导出文件名称映射    | 导出时的"内部名 → 文件名"映射表，数据交换桥接文件在 KCES 导出到 COM3D2.5 时生成   |
| `system.dat`              | System State 系统状态数据               |                                                                                   |
| `paths.dat`               | Resource Search Paths 资源搜索路径数据  |                                                                                   |


格式对照表

| COM3D2                          | KCES                                                                                                | 主要变化                                                                                                                                                                       |
|---------------------------------|-----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `.menu`（文本编译成二进制脚本） | `.menuassets`                                                                                       | 命令从**文本行**变成**枚举 + 参数数组**（`Command.Type` + `string[] args`）；一个容器装多个菜单；多了 `guid` / `id` / `parentId` / `attribute` / `defineTagNames` 等结构化字段 |
| `.mate`                         | `.materialassets`                                                                                   | 材质属性从**字符串属性名**变成**枚举 int**（如 `0 = _MainTex`、`100 = _Color`）；分成 texture / color / vector / float 四个数组；KCES2 追加 shader keyword 数组和 renderQueue  |
| `.pmat`                         | `.pmatassets`                                                                                       | 基本对应，多了 `targetId`                                                                                                                                                      |
| `.model`（含网格）              | `.model`+ 独立的 Unity Mesh `.mmesh`                                                                | **网格被拆出去了**，`.model` 只剩骨骼变换、骨骼名、材质引用、形态键、皮肤厚度、阴影标志                                                                                        |
| `.tex`                          | `.aba` 里的 Texture2D 扩展名还是 `.tex` 但是内容不一样了                                            | 变成真正的 Unity 纹理资源                                                                                                                                                      |
| `.phy` / `.col` / `.psk`        | `.dbconf` / `.dbcol` / `.db2conf` / `.dsbconf` / `.dsb2conf` / `.dslconf` / `.dsl2conf` / `.dslcol` | [附录](../../../附录/KCES%20物理文件格式速查.md)                                                                                                                               |
| `.preset`                       | 扩展名还是 `.preset` / `.perset` 但是内容不一样了                                                   | 内嵌 KCES 数据                                                                                                                                                                 |
| `.anm`                          | Unity AnimationClip 扩展名还是 `.anm` 但是内容不一样了                                              |                                                                                                                                                                                |
| `.arc`                          | `.ct` + `.aba`                                                                                      | 从自有压缩包变成 Unity AssetBundle + 独立索引                                                                                                                                  |
| 文件名查找                      | FNV-1a 64 位哈希查找                                                                                | 改名必须重算 `id`                                                                                                                                                              |
| `.nei`（Shift-Jis 编码）        | `.nei`（UTF-8 编码）+ `.nson`                                                                       | 与 COM3D2 共用格式                                                                                                                                                             |
| 无                              | `.undressdat` / `.undresspdat` / `.hitcheck` / `.sad` / `.brd` / `.enm`                             | KCES 新增                                                                                                                                                                      |



## 游戏查找文件时须注意


### 一切靠 FNV-1a 哈希查找

COM3D2 用文件名字符串查资源。KCES 里每个资源都有一个 `id`，是**文件名的 FNV-1a 64 位哈希**。

这带来两个坑：

- **大小写**：游戏在多处 `ToLower()` 转小写后再算哈希，但存储的 `id` 有时是按原始大小写算的。所以**文件名一律用全小写**，别自找麻烦。
- **改名必须重算哈希**。手写 JSON 改文件名而忘了改 `id`，资源就永远查不到。MeidoSerialization 默认会按当前文件名帮你重算，这是它的默认行为。

### 加载顺序

解析规则（`CatalogUtility.ParseAsCatalogAttribute`）：

| 文件名          | CatalogType | PackageType | priority | subName |
|-----------------|-------------|-------------|----------|---------|
| `parts`         | Parts       | Base        | 0        | —       |
| `parts_1`       | Parts       | BasePatch   | 1        | `parts` |
| `parts_mymod`   | Parts       | Plugin      | 0        | `mymod` |
| `parts_mymod_3` | Parts       | PluginPatch | 3        | `mymod` |
| `parts-extra`   | Parts       | ExtraBase   | 0        | —       |

加载顺序按 `catalogType` → `packageType` → `priority` → `name` 排序，**后加载的覆盖先加载的**，所以 `priority` 就是覆盖优先级。

### 打包的硬性规则

这几条踩了就是资源查不到：

1. **输出名必须全小写**。KCES 1.34.5 用小写注册、又用大小写敏感的方式比对，加了大写就读不到了。KCES2 似乎修复了，可以使用大写。
2. **KCES 1.34.5 只认名为 `<aba名>.menuassets` / `<aba名>.materialassets` 的文件，名字不一样则读不到**。
   - KCES2 改进了这点：`PartsCatalogAssetLoader` 通过 `catalog.GetFileNameListFromExtension(".menuassets")` 枚举，容器叫什么都能被读到。要兼容 KCES1 就还是老实对齐名字。
   - `.menuassets` 里面的 xxxx.menu 命名必须带 .menu，`.materialassets` 的 xxxx.mate 命名必须带 .mate。否则哈希不一致查不到。
3. MeidoSerialization 生成的 `.ct` 默认元数据是 `catalogType = Parts`、`packageType = Plugin`、`priority = 0`。要改（比如提高覆盖优先级）就把 `.ct` 转成 JSON 改再转回去。


## 简而言之

KCES 目前不支持读取散装文件，未来未知

最简 MOD 还是只需要

- **`.menuassets`**
- **`.tex`**

要改模型就

- **`.menuassets`**
- **`.model`**
- **`.mmesh`**

要改材质就

- **`.menuassets`**
- **`.materialassets`**
- **`.tex`**