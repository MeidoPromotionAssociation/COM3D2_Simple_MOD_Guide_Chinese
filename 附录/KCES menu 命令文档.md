# Menu.Command 完整参考文档

本文档基于 KCES2 1.36.0 源码分析，详细记录 `.menu` 文件中所有命令的用法。

由 deepseek-v4-pro max 与 claude-opus-5 max 校对多次完成。

## 文件格式概要

### KCES MessagePack 格式（.menuassets 容器内的 .menu）

KCES 使用 MessagePack 序列化格式。每个 `.menuassets` 是一个容器，内含多个 `Menu` 对象。每个 `Menu` 对象包含：

- **元数据字段**：`fileName`, `itemName`, `categoryText`, `infoText`, `priority`, `iconFileName` 等
- **commandList**：`Menu.Command[]` 数组，每个元素包含：
  - `type`: 整数（对应 `Menu.Command.Type` 枚举值）
  - `args`: 字符串数组（命令参数）

### 文本格式（.menu 源文件）

KCES 的 `.menu` 源文件（以及旧版 CM3D2/COM3D2）为纯文本，每行一条命令：

- 命令名（第一个 token）+ 参数（后续 token），空格/Tab 分隔
- 参数含空格时用双引号包裹：`"参数值"`
- 以 `/` 开头的行为注释
- 命令名不区分大小写（解析时自动 `ToLower()`）
- 文件以 `endcommand` 结束

源文件由编辑器编译后装入 `.menuassets`（MessagePack 容器），游戏运行时读取的是后者。

命令分为两类：

- **CompileType（编译时命令）**：在编译阶段提取为菜单元数据，不进入 commandList
- **Type（运行时命令）**：进入游戏后执行，存储在 `commandList` 中


### 如何编辑

请使用 [https://github.com/MeidoPromotionAssociation/KCES_MOD_EDITOR](https://github.com/MeidoPromotionAssociation/KCES_MOD_EDITOR)

---

## 一、Type 整数映射表

在 KCES MessagePack 格式中，`command.type` 以整数值存储。以下为完整映射：

| 整数值 | 枚举名                   | 说明               |
| :----: | ------------------------ | ------------------ |
|   0    | `additem`                | 添加物品/模型      |
|   1    | `anime`                  | 物品动画           |
|   2    | `animematerial`          | 材质动画           |
|   3    | `blendset`               | 变形集定义         |
|   4    | `bonemorph`              | 骨骼变形           |
|   5    | `color`                  | 材质颜色           |
|   6    | `commenttype`            | 注释               |
|   7    | `delitem`                | 删除物品           |
|   8    | `endcommand`             | 命令结束           |
|   9    | `ifcommand`              | 条件命令           |
|   10   | `length`                 | 头发长度           |
|   11   | `mancolor`               | 男性颜色           |
|   12   | `maskitem`               | 遮罩物品           |
|   13   | `node消去`               | 隐藏节点           |
|   14   | `node表示`               | 显示节点           |
|   15   | `nofloory`               | 禁地面碰撞         |
|   16   | `param2`                 | 第二参数           |
|   17   | `paramset`               | 参数集             |
|   18   | `prop`                   | 属性设置           |
|   19   | `saveitem`               | 保存物品           |
|   20   | `set`                    | 设置               |
|   21   | `setname`                | 设置名称           |
|   22   | `setslotitem`            | 设置槽位物品       |
|   23   | `shader`                 | 着色器变更         |
|   24   | `tex`                    | 纹理变更           |
|   25   | `useredit`               | 用户编辑           |
|   26   | `ver`                    | 版本号             |
|   27   | `アイテム`               | 引用子菜单         |
|   28   | `アイテムパラメータ`     | 物品参数           |
|   29   | `アイテム条件`           | 物品条件           |
|   30   | `アタッチポイントの設定` | Attach 点设置      |
|   31   | `テクスチャセット合成`   | 纹理集合成         |
|   32   | `テクスチャ合成`         | 纹理合成           |
|   33   | `テクスチャ乗算`         | 纹理乘算           |
|   34   | `テクスチャ変更`         | 纹理变更(别名)     |
|   35   | `パーツnode消去`         | 零件节点隐藏       |
|   36   | `パーツnode表示`         | 零件节点显示       |
|   37   | `マテリアル変更`         | 材质变更           |
|   38   | `リソース参照`           | 资源引用           |
|   39   | `半脱ぎ`                 | 半脱衣             |
|   40   | `delitemnewattach`       | 删除新 Attach 物品 |
|   41   | `partcolor`              | 部件颜色           |
|   42   | `partcolordef`           | 部件颜色定义       |
|   43   | `pattern`                | 图案               |
|   44   | `material`               | 材质属性           |
|   45   | `uv定義`                 | UV 定义            |
|   46   | `ほくろ合成`             | 痣合成             |
|   47   | `タトゥ合成`             | 纹身合成           |
|   48   | `ネイル合成`             | 指甲合成           |
|   49   | `gradacolordef`          | 渐变色定义         |
|   50   | `メイク合成`             | 化妆合成           |
|   51   | `ifdef`                  | 条件开始           |
|   52   | `elseifdef`              | 条件分支           |
|   53   | `endifdef`               | 条件结束           |
|   54   | `mugencolordef`          | 无限色定义         |
|   55   | `partcolorrgb`           | 部件颜色(RGB)      |
|   56   | `meshmorph`              | 网格变形           |
|   57   | `マテリアル参照`         | 材质引用           |
|   58   | `addbonemorph`           | 添加骨骼变形       |
|   59   | `乳首`                   | 乳头状态           |
|   60   | `adjcutoff`              | Cutout 调整        |
|   61   | `parthidemove`           | 部件隐藏/移动      |
|   62   | `房tex`                  | 发束纹理           |
|   63   | `munekagergb`            | 胸阴影(RGB)        |
|   64   | `mask消去`               | 遮罩删除           |
|   65   | `munekage`               | 胸阴影             |
|   66   | `そばかす合成`           | 雀斑合成           |
|   67   | `ちんこ`                 | 男性器官状态       |
|   68   | `ひげ合成`               | 胡须合成           |
|   69   | `しみ合成`               | 斑点合成           |
|   70   | `しわ合成`               | 皱纹合成           |
|   71   | `体毛合成`               | 体毛合成           |
|   72   | `cutout消去`             | Cutout 删除        |
|   73   | `タッチ範囲tex`          | 触摸范围纹理       |

---

## 二、CompileType 编译时命令

这些命令在菜单编译时被提取为 Menu 对象的属性，不产生运行时命令。
它们存储在 `Menu` 对象本身的字段中，而非 `commandList`。

| CompileType 名     | Menu 字段                    | 说明             |
| ------------------ | ---------------------------- | ---------------- |
| `メニューフォルダ` | (路径前缀)                   | 菜单资源搜索路径 |
| `name`             | `itemName`                   | 道具显示名称     |
| `category`         | `categoryText`               | MPN 分类         |
| `setumei`          | `infoText`                   | 道具说明文本     |
| `icon`             | `iconFileName`               | 菜单图标         |
| `icons`            | `iconFileName`               | 菜单图标(多文件) |
| `unsetitem`        | `isDelete=true`              | 标记为删除菜单   |
| `priority`         | `priority`                   | 显示优先级       |
| `color_set`        | `colorSetText`               | 颜色集关联       |
| `gender`           | `targetBodyType`/`attribute` | 性别限制         |
| `define`           | `defineTagNames`             | Define 标记      |
| `ver`              | `partsVer`                   | 格式版本         |
| `attribute`        | `attribute`                  | 属性标记         |
| `toelock`          | `toeLockSlotId`              | 脚趾锁定         |
| `formtex`          | `exportModelFormTextureName` | 形态纹理         |
| `腹揺れ対応`       | `isHarayureAvailable`        | 腹部摇摆         |
| `skirt_phys`       | `skirt_phys`                 | 裙子物理版本     |
| `colicon`          | (colvari 相关)               | 颜色图标         |
| `colreqdefine`     | (colvari 相关)               | 颜色需要 define  |
| `colvari`          | `colvariInfo`                | 颜色变化数据     |
| `colvarifile`      | `colvariFileNameExp`         | 颜色变化文件     |
| `filter`           | (编辑筛选)                   | 编辑过滤器       |
| `edit`             | `hideInEdit`                 | 编辑隐藏         |
| `ネイル合成`       | `preMulTexDatas`             | 预编译指甲       |
| `タトゥ合成`       | `preMulTexDatas`             | 预编译纹身       |
| `ほくろ合成`       | `preMulTexDatas`             | 预编译痣         |
| `そばかす合成`     | `preMulTexDatas`             | 预编译雀斑       |
| `ひげ合成`         | `preMulTexDatas`             | 预编译胡须       |
| `しみ合成`         | `preMulTexDatas`             | 预编译斑点       |
| `しわ合成`         | `preMulTexDatas`             | 预编译皱纹       |
| `体毛合成`         | `preMulTexDatas`             | 预编译体毛       |

> **验证状态说明**：游戏侧 `PartsMenuManager.CreatePartsMenuFromOldMenu`（旧版二进制 men 导入路径）可证实的映射有 `icon`/`icons`→`iconFileName`、`priority`、`color_set`→`colorSet`、`gender`（`man_only`→`TargetBodyType.Man`，`butler`→`Attribute.ManReccomend`）、`unsetitem`→`isDelete`，以及旧版路径下 `end`/`if` 会补全为 `endcommand`/`ifcommand`。其余行（`name`、`category`、`filter`、`edit`、`ver`、合成类等）是依据 `Menu` 类的字段（`Menu.cs:370-470`）做的对应推断——真正的文本 `.menu` 编译逻辑在 KCES2_ED 编辑器侧，游戏侧源码不含该编译器。

---

## 三、Type 运行时命令详解

以下命令在游戏运行时按 `commandList` 顺序执行。每个命令的参数以 `args[N]` 标注。 `args[0]` 代表第一个参数，`args[1]` 代表第二个参数。

---

### type=0: additem — 添加物品/模型

```json
{
  "type": 0,
  "args": ["Hair_Aho046.model", "hairAho", "アタッチ", "hairF", "アホ毛"]
}
```

| 参数      | 必需 | 说明                                                                                                                                                 |
| --------- | :--: | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| args[0]   |  ✅  | 模型文件名（如 `xxx.model`）                                                                                                                         |
| args[1]   | 可选 | SlotID（默认使用当前菜单的 `category`）。特殊值：`body`=加载身体, `handitemr`=右手持物, `handiteml`=左手持物                                         |
| args[2-4] | 可选 | Attach 方式：`ボーンにアタッチ <骨骼名>`（占 args[2-3]）或 `アタッチ <AttachSlot> <AttachName>`（占 args[2-4]）                                      |
| args[N]   | 可选 | `split:<SlotID>=<分割参数>&<SlotID>=<分割参数>...` — 模型分割（`x<0.5` / `y>0.3` / `bone1\|bone2\|...`，`x`/`y`/`z` 为平面方向，骨骼名用 `\|` 分隔） |

> 注意：`split` 参数格式为 `split:<SlotID>=<分割参数>...`（`split` 后紧跟冒号，源码取冒号后的内容解析，缺少冒号会抛数组越界异常）。源码匹配的是 args 数组中首个以 `split` 开头（不分大小写）的参数，位置不限。

**编辑器命令示例**：

```
additem
    Hair_Aho046.model
    hairAho
    アタッチ
    hairF
    アホ毛
```

---

### type=1: anime — 物品动画

```json
{ "type": 1, "args": ["accHead", "anim_name", "loop"] }
```

| 参数    | 必需 | 说明                                                                   |
| ------- | :--: | ---------------------------------------------------------------------- |
| args[0] |  ✅  | `TBody.SlotID` 槽位名（新 Attach 类 MPN 下经 `GetNewAttachSlot` 转换） |
| args[1] |  ✅  | 动画文件名（无扩展名自动补 `.anm`）                                    |
| args[2] | 可选 | `loop` = 循环播放                                                      |

**编辑器命令示例**：

```
anime
    accHead
    anim_name
    loop
```

---

### type=2: animematerial — 材质动画

```json
{ "type": 2, "args": ["wear", "0"] }
```

| 参数    | 必需 | 说明                                                                   |
| ------- | :--: | ---------------------------------------------------------------------- |
| args[0] |  ✅  | `TBody.SlotID` 槽位名（新 Attach 类 MPN 下经 `GetNewAttachSlot` 转换） |
| args[1] |  ✅  | 材质编号（整数）                                                       |

**编辑器命令示例**：

```
animematerial
    wear
    0
```

---

### type=3: blendset — 变形集定义

```json
{ "type": 3, "args": ["表情名", "变形键1", "0.5", "变形键2", "1.0"] }
```

| 参数     | 必需 | 说明                              |
| -------- | :--: | --------------------------------- |
| args[0]  |  ✅  | BlendSet 名称                     |
| args[1+] |  ✅  | 键值对交替：`<变形键名> <浮点值>` |

**编辑器命令示例**：

```
blendset
    表情名
    变形键1
    0.5
    变形键2
    1.0
```

---

### type=4: bonemorph — 骨骼变形

**8 参数格式**（仅位置）：

```json
{ "type": 4, "args": ["属性名", "骨骼名", "x1", "y1", "z1", "x2", "y2", "z2"] }
```

**9 参数格式**（指定类型）：

```json
{
  "type": 4,
  "args": ["pos", "属性名", "骨骼名", "x1", "y1", "z1", "x2", "y2", "z2"]
}
```

| 参数      | 必需 | 说明                                                                          |
| --------- | :--: | ----------------------------------------------------------------------------- |
| args[0]   |  ✅  | 类型（`pos`/`rot`/`scl`，比较前 `ToLower()`；8 参数格式省略则按 `pos` 处理）  |
| args[1]   |  ✅  | 属性名（`strPropName`，即驱动该变形的 MPN 属性名，如 `MayuY`、`EyeBallSclX`） |
| args[2]   |  ✅  | 骨骼名（`trBone.name`）                                                       |
| args[3-5] |  ✅  | 最小值增量 (x, y, z)                                                          |
| args[6-8] |  ✅  | 最大值增量 (x, y, z)                                                          |

> 注意：两组向量是**相对骨骼当前 local 变换的增量**而非绝对值（`m_vAddMin = trBone.localPosition + f_fAddMin`，`TMorphBone.cs:329-357`）。属性名与骨骼名同时参与 `Find` 匹配，任一不符则找不到目标、静默无效。8 参数格式下 args[0] 是属性名、args[1] 是骨骼名（`ChangeMorphPosValue(args[0], args[1], …)`），不是「骨骼名 + 变形名」。

**编辑器命令示例**（8 参数格式）：

```
bonemorph
    MayuY
    Mayu_R
    0.0
    0.0
    0.0
    1.0
    0.0
    0.0
```

**编辑器命令示例**（9 参数格式）：

```
bonemorph
    pos
    MayuY
    Mayu_R
    0.0
    0.0
    0.0
    1.0
    0.0
    0.0
```

---

### type=5: color — 材质颜色

```json
{ "type": 5, "args": ["head", "0", "_Color", "255", "128", "64", "255"] }
```

| 参数      | 必需 | 说明                  |
| --------- | :--: | --------------------- |
| args[0]   |  ✅  | SlotID                |
| args[1]   |  ✅  | 材质编号              |
| args[2]   |  ✅  | 属性名（如 `_Color`） |
| args[3-5] |  ✅  | RGB 值（0-255）       |
| args[6]   |  ✅  | Alpha 值（0-255）     |

**编辑器命令示例**：

```
color
    head
    0
    _Color
    255
    128
    64
    255
```

---

### type=6: commenttype — 注释

```json
{ "type": 6, "args": ["注释内容"] }
```

无实际游戏效果，仅作文本注释。

**编辑器命令示例**：

```
commenttype
    这是注释内容
```

---

### type=7: delitem — 删除物品

```json
{"type": 7, "args": []}
{"type": 7, "args": ["wear"]}
```

| 参数    | 必需 | 说明                                  |
| ------- | :--: | ------------------------------------- |
| args[0] | 可选 | SlotID（默认使用当前菜单的 category） |

**编辑器命令示例**：

```
delitem
    wear
```

---

### type=8: endcommand — 命令结束

```json
{ "type": 8, "args": [] }
```

标记命令列表执行结束。

**编辑器命令示例**：

```
endcommand
```

---

### type=9: ifcommand — 条件命令

```json
{
  "type": 9,
  "args": [
    "maidprop[xxx]",
    "==",
    "nothing",
    "?",
    "setprop[yyy]",
    "=",
    "getprop[zzz]"
  ]
}
```

| 参数    | 必需 | 说明              |
| ------- | :--: | ----------------- |
| args[0] |  ✅  | `maidprop[<MPN>]` |
| args[1] |  ✅  | `==`              |
| args[2] |  ✅  | `nothing`         |
| args[3] |  ✅  | `?`               |
| args[4] |  ✅  | `setprop[<MPN>]`  |
| args[5] |  ✅  | `=`               |
| args[6] |  ✅  | `getprop[<MPN>]`  |

如果指定 MPN 槽位为空，则从另一个 MPN 槽位复制物品。

**编辑器命令示例**：

```
ifcommand
    maidprop[wear]
    ==
    nothing
    ?
    setprop[accHead]
    =
    getprop[wear]
```

---

### type=10: length — 头发长度

```json
{
  "type": 10,
  "args": [
    "hairF",
    "前髪1",
    "fbrother",
    "03a_yure_hair_h_",
    "0.7",
    "0.8",
    "1.0",
    "1.3",
    "1.2",
    "1.0"
  ]
}
```

固定 10 个参数：

| 参数      | 必需 | 说明                                                                                                                                     |
| --------- | :--: | ---------------------------------------------------------------------------------------------------------------------------------------- |
| args[0]   |  ✅  | SlotID                                                                                                                                   |
| args[1]   |  ✅  | 长度组名（如 `前髪` / `後髪`，f_strGroupName）                                                                                           |
| args[2]   |  ✅  | 骨骼搜索类型（仅 `fbrother`=首个兄弟 / `fchild`=首个子级 / `all`=全部，**区分大小写**精确比较，其它值 Assert 报错；f_strBoneSearchType） |
| args[3]   |  ✅  | 骨骼名（f*strBoneName，如 `03a_yure_hair_h*`；`_`会被替换为`._` 参与正则匹配）                                                           |
| args[4-6] |  ✅  | 缩放最小值向量 (x, y, z)（f_vScaleMin）                                                                                                  |
| args[7-9] |  ✅  | 缩放最大值向量 (x, y, z)（f_vScaleMax）                                                                                                  |

**编辑器命令示例**：

```
length
    hairR
    後髪
    fbrother
    _yure_hair_h_R*
    0.8
    0.9
    1.0
    1.2
    1.1
    1.0
```

---

### type=11: mancolor — 男性颜色

```json
{ "type": 11, "args": ["body", "0", "_Color", "200", "150", "100"] }
```

| 参数      | 必需 | 说明                                          |
| --------- | :--: | --------------------------------------------- |
| args[0]   |  ✅  | SlotID（占位，源码中不使用）                  |
| args[1]   |  ✅  | 材质编号（占位，源码中不使用）                |
| args[2]   |  ✅  | 属性名（占位，源码中不使用）                  |
| args[3-5] |  ✅  | RGB 值（0-255），Alpha 固定为 1（不可自定义） |

> 注意：源码中仅使用 `args[3]`~`args[5]` 作为 RGB 值，Alpha 硬编码为 `1f`（完全不透明）。`args[0]`~`args[2]` 虽然必须提供（占位），但运行时被完全忽略。

**编辑器命令示例**：

```
mancolor
    body
    0
    _Color
    200
    150
    100
```

---

### type=12: maskitem — 遮罩物品

```json
{ "type": 12, "args": ["chikubi"] }
```

| 参数    | 必需 | 说明                                             |
| ------- | :--: | ------------------------------------------------ |
| args[0] |  ✅  | 作为遮罩的槽位名（`TBody.SlotID`，如 `chikubi`） |

将当前菜单 category 对应的槽位添加遮罩（`AddMask(category, args[0])`）。args[0] 必须是槽位名（存在于 `TBody.hashSlotName`，`TBody.cs:1646-1655`），纹理/材质名会被静默忽略。

**编辑器命令示例**：

```
maskitem
    chikubi
```

---

### type=13: node消去 — 隐藏节点

```json
{"type": 13, "args": ["ForeTwist5"]}
{"type": 13, "args": ["节点名", "slot=wear"]}
```

| 参数     | 必需 | 说明                                     |
| -------- | :--: | ---------------------------------------- |
| args[0]  |  ✅  | 节点名称（支持部分匹配，`_ALL_` = 全部） |
| args[1+] | 可选 | `slot=<SlotID>`（默认当前 category）     |

**编辑器命令示例**：

```
node消去
    ForeTwist5
```

---

### type=14: node表示 — 显示节点

```json
{ "type": 14, "args": ["节点名"] }
```

参数同 `node消去`，但作用相反：显示被隐藏的节点。

**编辑器命令示例**：

```
node表示
    ForeTwist5
```

---

### type=15: nofloory — 禁地面碰撞

```json
{ "type": 15, "args": ["标志", "shoes"] }
```

| 参数    | 必需 | 说明                                     |
| ------- | :--: | ---------------------------------------- |
| args[0] |  ✅  | 占位符（内容无意义，但参数个数必须为 2） |
| args[1] |  ✅  | SlotID                                   |

> 注意：源码直接读取 `command.args[1]` 作为 SlotID 并设置 `m_bHitFloorY = false`，`args[0]` 内容完全被忽略；但必须写满 2 个参数，只写 1 个会数组越界。

**编辑器命令示例**：

```
nofloory
    1
    shoes
```

---

### type=16: param2 — 第二参数

```json
{ "type": 16, "args": ["wear", "参数名", "参数值"] }
```

| 参数    | 必需 | 说明   |
| ------- | :--: | ------ |
| args[0] |  ✅  | SlotID |
| args[1] |  ✅  | 参数名 |
| args[2] |  ✅  | 参数值 |

**编辑器命令示例**：

```
param2
    wear
    param_name
    param_value
```

---

### type=17: paramset — 参数集

```json
{ "type": 17, "args": ["参数集名", "键1", "值1", "键2", "值2"] }
```

| 参数     | 必需 | 说明                                                   |
| -------- | :--: | ------------------------------------------------------ |
| args[0+] |  ✅  | 所有参数拼接为一个字符串后传入 `NewParamSet(...)` 处理 |

> 注意：源码将 `type.ToString()` + 所有 `args` 以空格拼接后传给 `Face.NewParamSet(...)`，本身不做键值对解析。具体的参数名/参数值格式由 `NewParamSet` 内部决定。

**编辑器命令示例**：

```
paramset
    参数集名
    key1
    value1
    key2
    value2
```

---

### type=18: prop — 属性设置

```json
{ "type": 18, "args": ["EyeBallSclX", "48"] }
```

| 参数    | 必需 | 说明                                           |
| ------- | :--: | ---------------------------------------------- |
| args[0] |  ✅  | MPN 枚举值（**区分大小写**，如 `EyeBallSclX`） |
| args[1] |  ✅  | 编号（整数，写入该属性的值，受 min/max 截断）  |

> 注意：与其它命令的 MPN 解析（`Parse.TryParse`，大小写不敏感）不同，此路径走 `Maid.SetProp(string,int)` 内的 `Enum.Parse(MPN)`（`Maid.cs:638`），**大小写敏感**，写错会抛异常并落到 `null_mpn`。

**编辑器命令示例**：

```
prop
    EyeBallSclX
    48
```

---

### type=19: saveitem — 保存物品

```json
{ "type": 19, "args": [] }
```

> ⚠️ **注意**：在 `PartsMenuManager.Exec()` 中 **无运行时处理器**，执行时被静默忽略。此命令为旧版遗留，在 KCES 中不生效。

保存当前物品状态（仅在旧版格式中有效）。

**编辑器命令示例**：

```
saveitem
```

---

### type=20: set — 设置

```json
{ "type": 20, "args": ["参数名", "值"] }
```

> ⚠️ **注意**：在 `PartsMenuManager.Exec()` 中 **无运行时处理器**，执行时被静默忽略。此命令为旧版遗留，在 KCES 中不生效。

**编辑器命令示例**：

```
set
    parameter_name
    value
```

---

### type=21: setname — 设置名称

```json
{ "type": 21, "args": ["名称"] }
```

> ⚠️ **注意**：在 `PartsMenuManager.Exec()` 中 **无运行时处理器**，执行时被静默忽略。此命令为旧版遗留，在 KCES 中不生效。

**编辑器命令示例**：

```
setname
    套装名称
```

---

### type=22: setslotitem — 设置槽位物品

```json
{ "type": 22, "args": ["acchead", "12345"] }
```

| 参数    | 必需 | 说明                                                               |
| ------- | :--: | ------------------------------------------------------------------ |
| args[0] |  ✅  | MPN 枚举值（**区分大小写**，如 `acchead`）                         |
| args[1] |  ✅  | 数值（uint 文本，`(int)uint.Parse` 后写入该属性，受 min/max 截断） |

> 注意：与 `prop`（type=18）走同一处理路径（`Maid.SetProp(string,int)`），实际是把数值写入 MPN 属性而非设置装备文件名；MPN 名同样大小写敏感。

**编辑器命令示例**：

```
setslotitem
    acchead
    12345
```

---

### type=23: shader — 着色器变更

```json
{ "type": 23, "args": ["wear", "0", "shader_name"] }
```

| 参数    | 必需 | 说明       |
| ------- | :--: | ---------- |
| args[0] |  ✅  | SlotID     |
| args[1] |  ✅  | 材质编号   |
| args[2] |  ✅  | 着色器名称 |

**编辑器命令示例**：

```
shader
    wear
    0
    shader_name
```

---

### type=24: tex — 纹理变更

```json
{
  "type": 24,
  "args": [
    "mizugi",
    "0",
    "_MainTex",
    "crc_mizugi004_1.tex",
    "MUGEN_COLOR",
    "mugen",
    "ColorA"
  ]
}
```

```json
{
  "type": 24,
  "args": [
    "hairf",
    "0",
    "_MainTex",
    "crc_hair_f016_base.tex",
    "GRADA_COLOR|MUGEN_COLOR",
    "grada|mugen",
    "色",
    "髪:LinkColor=hairf,影"
  ]
}
```

| 参数    | 必需 | 说明                                                                                                                                                                            |
| ------- | :--: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| args[0] |  ✅  | SlotID                                                                                                                                                                          |
| args[1] |  ✅  | 材质编号（支持 `MPN=值&MPN=值` 条件格式）                                                                                                                                       |
| args[2] |  ✅  | 纹理属性名（如 `_MainTex`, `_ShadowTex`）                                                                                                                                       |
| args[3] |  ✅  | 纹理文件名                                                                                                                                                                      |
| args[4] | 可选 | 无限色类型：`MUGEN_COLOR` / `GRADA_COLOR` / `PART_COLOR`（可 `\|` 连接副类型，如 `GRADA_COLOR\|MUGEN_COLOR`）                                                                   |
| args[5] | 可选 | 颜色引用模式：`mugen` / `grada` / `grada\|mugen`（决定 args[6] 按哪种颜色系统解析）                                                                                             |
| args[6] | 可选 | 颜色定义名（可附带 `:ALPHA_TYPE=百分比` 后缀控制 alpha，如 `色` 或 `1影:ALPHA_TEX=50`）                                                                                         |
| args[7] | 可选 | 保存层标签 `saveLayerTag`（空时默认 `main`；无限色生效且 `parts_COLOR != NONE` 时支持 `<标签>:LinkColor=<名>` / `<标签>:LinkColorAlpha=<名>` 后缀，如 `髪:LinkColor=hairf,影`） |

> 注意：args[1] 的 `MPN=值&MPN=值` 条件格式经 `GetSelectValueFromMPN` 解析——所有条件均不匹配当前父 MPN 时会触发 Assert 并返回 null，随后 `int.Parse(null)` 抛异常。建议条件集中保证有默认匹配项。

**编辑器命令示例**：

```
tex
    hairf
    0
    _MainTex
    crc_hair_f016_base.tex
    GRADA_COLOR|MUGEN_COLOR
    grada|mugen
    色
    髪:LinkColor=hairf,影
```

---

### type=25: useredit — 用户编辑

```json
{
  "type": 25,
  "args": [
    "前髪裏耳",
    "Material",
    "head",
    "3",
    "_ZTest",
    "UnityEngine.Rendering.CompareFunction",
    "4"
  ]
}
```

| 参数    | 必需 | 说明                                                                                                                |
| ------- | :--: | ------------------------------------------------------------------------------------------------------------------- |
| args[0] |  ✅  | 保存标签（`saveTag`，写入编辑存档用于还原）                                                                         |
| args[1] |  ✅  | 编辑类型（比较前 `ToLower()`，当前仅支持 `Material`，其他值会被静默忽略）                                           |
| args[2] |  ✅  | 目标 SlotID（如 `head`, `wear`）                                                                                    |
| args[3] |  ✅  | 材质编号                                                                                                            |
| args[4] |  ✅  | 属性名（如 `_ZTest`, `_Shininess`）                                                                                 |
| args[5] |  ✅  | 属性类型，仅识别 `DEFINE` / `TEX_OFFSET` / `TEX_SCALE` / `Color` 四个关键字，**其它任何写法一律按 `SetFloat` 处理** |
| args[6] |  ✅  | 属性值                                                                                                              |

属性类型的实际行为（`MaterialMgr.SetMaterialProperty`，`MaterialMgr.cs:1465-1515`）：

- `DEFINE`：值为 `0` 时 `DisableKeyword(属性名)`，为 `1` 时 `EnableKeyword(属性名)`
- `TEX_OFFSET` / `TEX_SCALE`：值写作 `u:v`，调用 `SetTextureOffset` / `SetTextureScale`
- `Color`：值写作 `r:g:b:a`（0~1 浮点），调用 `SetColor`
- 其它：`SetFloat(属性名, float.Parse(值))`——所以旧式写法 `System.Single`、`UnityEngine.Rendering.CompareFunction` 都只是走到这个分支，类型名本身并不被解析

> 注意：属性名 `_ZTest2` 会被改写成 `_ZTest`，且值 `1` 变 `4`、其它变 `8`。属性在 shader 上不存在时只记录错误、不中断执行。

**编辑器命令示例**：

```
useredit
    前髪裏耳
    Material
    head
    3
    _ZTest
    System.Single
    4
```

---

### type=26: ver — 版本号

```json
{ "type": 26, "args": ["200"] }
```

| 参数    | 必需 | 说明                                                              |
| ------- | :--: | ----------------------------------------------------------------- |
| args[0] |  ✅  | 版本号（1 参数格式）；或槽位名（2 参数格式，此时 args[1]=版本号） |

默认版本号 `100`。源码逻辑：`args.Length==2` 时取 `args[1]` 为版本号，否则取 `args[0]`。

**编辑器命令示例**：

```
ver
    200
```

---

### type=27: アイテム — 引用子菜单

```json
{"type": 27, "args": ["xxx_i_.menu"]}
{"type": 27, "args": ["xxx.menu", "colvari=a", "sub=0-2"]}
```

| 参数     | 必需 | 说明                                                              |
| -------- | :--: | ----------------------------------------------------------------- |
| args[0]  |  ✅  | 子菜单文件名                                                      |
| args[1+] | 可选 | 键值对：`colvari=<値>`, `def=<DEFINE>`, `mpn=<MPN>`, `sub=<范围>` |

> 键值对细节：`colvari=<单字符>` 时自动由当前文件名生成目标菜单名（把当前名中的 `_i_` 替换为 `_color_<字符>_i_`），`colvari=<完整文件名>` 则直接使用该文件名；`sub=all` 表示全部子槽（0 ~ TBody.SUB_SLOT_NO-1，当前 `SUB_SLOT_NO=5` 即 0~4），`sub=<起>-<止>` 指定范围；`def=` / `mpn=` 覆盖子菜单自身的 define 与 MPN。colvari 命中的子物品还会执行 `ExecColvari` 刷新无限色。

**编辑器命令示例**：

```
アイテム
    xxx_i_.menu
    colvari=a
    sub=0-2
```

---

### type=28: アイテムパラメータ — 物品参数

```json
{ "type": 28, "args": ["wear", "参数名", "参数值"] }
```

恰好 3 个参数。在所有其他命令执行完后统一设置。

> 注意：此命令的枚举名为 `アイテムパラメータ`（`Menu.cs:546` 写作 `アイテムパラメータ`，即长音符在「メ」与「タ」之间，末尾**不带**长音）。`アイテム条件`（type=29）格式 3 的固定 token `のアイテムパラメータの`（源码硬编码比较，`PartsMenuManager.cs:496`）与之拼写一致。

**编辑器命令示例**：

```
アイテムパラメータ
    wear
    param_name
    param_value
```

---

### type=29: アイテム条件 — 物品条件

**格式1**: 检查是否有物品（`有る` / `無い` 两种判定）

```json
{"type": 29, "args": ["wear", "に何か", "有る", "なら", "xxx.menu"]}
{"type": 29, "args": ["wear", "に何か", "無い", "なら", "xxx_del.menu"]}
```

| 参数    | 必需 | 说明                                                  |
| ------- | :--: | ----------------------------------------------------- |
| args[0] |  ✅  | TBody.SlotID 槽位名                                   |
| args[1] |  ✅  | 固定 `に何か`                                         |
| args[2] |  ✅  | `有る`（槽位有物品时执行）或 `無い`（槽位为空时执行） |
| args[3] |  ✅  | 固定 `なら`                                           |
| args[4] |  ✅  | 条件成立时执行的菜单文件                              |

**格式2**: 检查模型文件名（`m_strModelFileName == args[2]`，区分大小写）

```json
{ "type": 29, "args": ["wear", "が", "xxx.model", "なら", "yyy.menu"] }
```

**格式3**: 检查物品参数

```json
{
  "type": 29,
  "args": [
    "wear",
    "のアイテムパラメータの",
    "param",
    "が",
    "value",
    "なら",
    "xxx.menu"
  ]
}
```

**编辑器命令示例**（检查是否有物品）：

```
アイテム条件
    wear
    に何か
    有る
    なら
    xxx.menu
```

**编辑器命令示例**（检查槽位为空）：

```
アイテム条件
    wear
    に何か
    無い
    なら
    xxx_del.menu
```

**编辑器命令示例**（检查文件名）：

```
アイテム条件
    wear
    が
    xxx.model
    なら
    yyy.menu
```

---

### type=30: アタッチポイントの設定 — Attach 点设置

```json
{
  "type": 30,
  "args": ["点名", "0.0004", "1.6889", "0.008951", "0", "-90", "0"]
}
```

| 参数      | 必需 | 说明                                                                                                      |
| --------- | :--: | --------------------------------------------------------------------------------------------------------- |
| args[0]   |  ✅  | Attach 点名                                                                                               |
| args[1-3] |  ✅  | 位置 (X, Y, Z)                                                                                            |
| args[4-6] |  ✅  | 旋转角度 (RotX, RotY, RotZ)（欧拉角）。需完整提供 7 个参数；省略会触发 `Debug.LogError` 并回退为 identity |

> 注意：`args.Length < 4` 时报错且不执行；参数个数恰好为 4~6 或大于 7 时会报"角度指定がありません"并回退为 identity 旋转（仍会执行）；只有 7 参数（位置 args[1-3] + 欧拉角 args[4-6]）是完整形式。

**编辑器命令示例**：

```
アタッチポイントの設定
    点名
    0.0004
    1.6889
    0.008951
    0
    -90
    0
```

---

### type=31: テクスチャセット合成 — 纹理集合成

```json
{
  "type": 31,
  "args": [
    "hairf",
    "0",
    "_MainTex",
    "100",
    "tex.tex",
    "AlphaDstAlpha",
    "GRADA_COLOR|MUGEN_COLOR",
    "100",
    "色:ALPHA_TEX=100",
    "1影"
  ]
}
```

**删除模式**：

```json
{ "type": 31, "args": ["del", "head", "5", "_MainTex", "2000"] }
```

**非删除模式参数**：

| 参数     | 必需 | 说明                                                                                                                                                |
| -------- | :--: | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| args[0]  |  ✅  | SlotID                                                                                                                                              |
| args[1]  |  ✅  | 材质编号（与 `tex` 相同，支持 `MPN=值&MPN=值` 条件格式）                                                                                            |
| args[2]  |  ✅  | UV 属性名（如 `_MainTex`）                                                                                                                          |
| args[3]  |  ✅  | 层编号（整数，如 `100`）                                                                                                                            |
| args[4]  |  ✅  | 纹理文件名                                                                                                                                          |
| args[5]  |  ✅  | 混合模式（`GameUtility.SystemMaterial`，如 `Alpha`, `Multiply`, `AddNormal`, `AlphaDstAlpha`）。非删除模式下源码无长度检查，少于 6 个参数会数组越界 |
| args[6]  | 可选 | 无限色类型（`MUGEN_COLOR` / `GRADA_COLOR\|MUGEN_COLOR` / `PART_COLOR`）                                                                             |
| args[7]  | 可选 | 组内层号（`f_nLayNoInGroup`，`f_bTexGroup=true` 时生效）                                                                                            |
| args[8]  | 可选 | alpha 参数（`名称:ALPHA_TYPE=百分比`，如 `色:ALPHA_TEX=50`）                                                                                        |
| args[9]  | 可选 | 保存层标签（`f_strSaveTag`，与 `tex` 命令的 `saveLayerTag` 同类；无限色生效时同时作为纹理键 `tag` 的基础，被展开为 `tag:材质编号:属性名`）          |
| args[10] | 可选 | TransTex 数据                                                                                                                                       |

**删除模式参数**：

| 参数    | 必需 | 说明       |
| ------- | :--: | ---------- |
| args[0] |  ✅  | `del`      |
| args[1] |  ✅  | SlotID     |
| args[2] |  ✅  | 材质编号   |
| args[3] |  ✅  | UV 属性名  |
| args[4] |  ✅  | ID（整数） |

**编辑器命令示例**（非删除模式）：

```
テクスチャセット合成
    hairf
    0
    _MainTex
    100
    tex.tex
    AlphaDstAlpha
    GRADA_COLOR|MUGEN_COLOR
    100
    色:ALPHA_TEX=100
    1影
```

**编辑器命令示例**（删除模式）：

```
テクスチャセット合成
    del
    head
    5
    _MainTex
    2000
```

---

### type=32: テクスチャ合成 — 纹理合成

与 `テクスチャセット合成`（type=31）共享同一处理器，参数格式相同，但运行时不传递颜色参数（`infColParam=null`）。支持删除模式和非删除模式。

**非删除模式**：

```json
{
  "type": 32,
  "args": [
    "body",
    "0",
    "_MainTex",
    "100",
    "tex.tex",
    "Alpha",
    "MUGEN_COLOR",
    "色"
  ]
}
```

**删除模式**：

```json
{ "type": 32, "args": ["del", "body", "0", "_MainTex", "500"] }
```

> 注意：与 `テクスチャセット合成` 的区别在于颜色系统不会实际生效（infColParam 为 null），适用于不需要无限色/渐变色控制的纹理合成。参数格式与 `テクスチャセット合成` 相同，详见 type=31 的参数表。

**编辑器命令示例**（非删除模式）：

```
テクスチャ合成
    body
    0
    _MainTex
    100
    tex.tex
    Alpha
```

**编辑器命令示例**（删除模式）：

```
テクスチャ合成
    del
    body
    0
    _MainTex
    500
```

---

### type=33: テクスチャ乗算 — 纹理乘算

```json
{ "type": 33, "args": ["body", "0", "_MainTex", "100", "tex.tex", "Multiply"] }
```

> ⚠️ **注意**：在 `PartsMenuManager.Exec()` 中 **无运行时处理器**，执行时被静默忽略。`Exec()` 中的纹理合成条件（`:1021`）仅匹配 `テクスチャ合成` 和 `テクスチャセット合成`，不包含 `テクスチャ乗算`。全代码库搜索 `テクスチャ乗算` 仅命中枚举定义（`Menu.cs:551`）一处。此命令在当前版本不生效。

**编辑器命令示例**：

```
テクスチャ乗算
    body
    0
    _MainTex
    100
    tex.tex
    Multiply
```

---

### type=34: テクスチャ変更 — 纹理变更（tex 的别名）

与 `tex`（type=24）完全相同的行为。

**编辑器命令示例**：

```
テクスチャ変更
    head
    0
    _MainTex
    crc_head001.tex
```

---

### type=35: パーツnode消去 — 零件节点隐藏

```json
{ "type": 35, "args": ["glove", "Kata"] }
{"type": 35, "args": ["目标槽名", "节点名", "slot=accHead"]}
```

| 参数     | 必需 | 说明                                                                    |
| -------- | :--: | ----------------------------------------------------------------------- |
| args[0]  |  ✅  | 目标槽位名（被隐藏部件所在的槽，`TBody.SlotID` 名，如 `glove`、`wear`） |
| args[1]  |  ✅  | 节点/骨骼匹配名（`IndexOf` 部分匹配，`_ALL_` = 全部）                   |
| args[2+] | 可选 | `slot=<SlotID>`（执行命令的物品所在槽，默认使用当前 category）          |

> 注意：执行命令的物品所在槽**不是** args[0]，而是通过可选的 `slot=xxx` 参数指定，未指定时使用当前菜单的 category。源码行为：args[0] 是被遮蔽部件所在的**目标槽位名**（写入 `m_dicDelNodeParts` 字典的键，读取侧以其它槽的 `Category` 查询，`TBody.cs:2338-2358`）；args[1] 才是节点/骨骼名——在自身物品的节点树中做 `IndexOf` 部分匹配，命中的节点名用于隐藏目标槽中同名骨骼（`TBodySkin.cs:883-907`）。因此 args[0] 写成非槽位名（如旧式 `パーツnode消去 Kata glove`）时该条永不命中、完全无效；KCES 中应写为 `パーツnode消去 glove Kata`。

**编辑器命令示例**：

```
パーツnode消去
    glove
    Kata
```

---

### type=36: パーツnode表示 — 零件节点显示

```json
{ "type": 36, "args": ["glove", "Forearm"] }
```

参数同 `パーツnode消去`，作用相反：显示被隐藏的节点。详见 type=35 的参数说明。

**编辑器命令示例**：

```
パーツnode表示
    glove
    Forearm
```

---

### type=37: マテリアル変更 — 材质变更

```json
{ "type": 37, "args": ["accHead", "0", "crc_acchead003_1.mate"] }
```

| 参数    | 必需 | 说明                  |
| ------- | :--: | --------------------- |
| args[0] |  ✅  | SlotID                |
| args[1] |  ✅  | 材质编号              |
| args[2] |  ✅  | 材质文件名（`.mate`） |

> 新 Attach 类 MPN（`accAcc1`~`accAcc24`）下 args[0] 的虚拟槽会经 `GetNewAttachSlot` 转换为真实槽位。

**编辑器命令示例**：

```
マテリアル変更
    accHead
    0
    crc_acchead003_1.mate
```

---

### type=38: リソース参照 — 资源引用

```json
{ "type": 38, "args": ["半脱ぎ", "xxx_hadake_i_.menu"] }
```

| 参数    | 必需 | 说明             |
| ------- | :--: | ---------------- |
| args[0] |  ✅  | 引用标签名       |
| args[1] |  ✅  | 引用的菜单文件名 |

**编辑器命令示例**：

```
リソース参照
    半脱ぎ
    xxx_hadake_i_.menu
```

---

### type=39: 半脱ぎ — 半脱衣

```json
{ "type": 39, "args": ["xxx_hadake_i_.menu"] }
```

| 参数    | 必需 | 说明             |
| ------- | :--: | ---------------- |
| args[0] |  ✅  | 引用的菜单文件名 |

等价于 `リソース参照 半脱ぎ <文件名>`。

**编辑器命令示例**：

```
半脱ぎ
    xxx_hadake_i_.menu
```

---

### type=40: delitemnewattach — 删除新 Attach 物品

```json
{ "type": 40, "args": [] }
```

无参数。卸载新 Attach 方式的部件。

**编辑器命令示例**：

```
delitemnewattach
```

---

### type=41: partcolor — 部件颜色

```json
{"type": 41, "args": ["SlotID", "材质编号", "UV属性名", "纹理名", "id1.tex&id2.tex", "ColorA", ...]}
```

| 参数     | 必需 | 说明                                                                                                                       |
| -------- | :--: | -------------------------------------------------------------------------------------------------------------------------- |
| args[0]  |  ✅  | SlotID                                                                                                                     |
| args[1]  |  ✅  | 材质编号                                                                                                                   |
| args[2]  |  ✅  | UV 属性名                                                                                                                  |
| args[3]  |  ✅  | 纹理文件名（主纹理）                                                                                                       |
| args[4]  |  ✅  | ID 纹理文件名列表（`&` 分隔，`_` 表示空位；partcolorrgb 时作为 RGB 通道查找纹理）                                          |
| args[5+] | 可选 | 颜色名列表（引用 `partcolordef` 中定义的颜色名），可带 `名称:ALPHA_ENUM` alpha 后缀（如 `髪:ALPHA_TEX`，多个时取最后一个） |

**编辑器命令示例**：

```
partcolor
    wear
    0
    _MainTex
    crc_dress001_color.tex
    crc_dress001_id1.tex&crc_dress001_id2.tex
    ColorA
    ColorB
```

---

### type=42: partcolordef — 部件颜色定义

```json
{
  "type": 42,
  "args": [
    "glove",
    "ColorA",
    "H=160,S=160,L=256,C=101,SH=156,SS=64,SL=200,SC=100,T=221",
    "ColorB",
    "..."
  ]
}
```

| 参数     | 必需 | 说明                                                                               |
| -------- | :--: | ---------------------------------------------------------------------------------- |
| args[0]  |  ✅  | SlotID                                                                             |
| args[1+] |  ✅  | 颜色名和 HSL 数据交替：`<名称> <H=..,S=..,L=..,C=..,SH=..,SS=..,SL=..,SC=..,T=..>` |

HSL 参数字段（源码字段映射，`PartsMenuManager.ParseInfColor`）：

- `H`=主色相 (m_nMainHue), `S`=主彩度 (m_nMainChroma), `L`=主亮度 (m_nMainBrightness), `C`=主对比度 (m_nMainContrast)
- `SH`=阴影色相 (m_nShadowHue), `SS`=阴影彩度 (m_nShadowChroma), `SL`=阴影亮度 (m_nShadowBrightness), `SC`=阴影对比度 (m_nShadowContrast)
- `T`=阴影率 (m_nShadowRate)

**编辑器命令示例**：

```
partcolordef
    glove
    ColorA
    H=160,S=160,L=256,C=101,SH=156,SS=64,SL=200,SC=100,T=221
    ColorB
    H=0,S=0,L=255,C=100,SH=0,SS=0,SL=200,SC=100,T=100
```

---

### type=43: pattern — 图案

```json
{"type": 43, "args": ["tex.tex", "MUGEN_COLOR"]}
{"type": 43, "args": ["del"]}
```

| 参数    | 必需 | 说明             |
| ------- | :--: | ---------------- |
| args[0] |  ✅  | 纹理名或 `del`   |
| args[1] | 可选 | PARTS_COLOR 枚举 |

> ⚠️ **注意**：源码中 `pattern` 命令仅解析 `PARTS_COLOR` 枚举值后 **未做任何后续处理**，是一条残留/失效命令，运行时实际不生效。编译时可能用于预编译提取数据。

**编辑器命令示例**：

```
pattern
    tex.tex
    MUGEN_COLOR
```

---

### type=44: material — 材质属性

```json
{ "type": 44, "args": ["SlotID", "材质编号", "属性名", "属性类型", "属性值"] }
```

| 参数    | 必需 | 说明                                                                                                     |
| ------- | :--: | -------------------------------------------------------------------------------------------------------- |
| args[0] |  ✅  | SlotID                                                                                                   |
| args[1] |  ✅  | 材质编号                                                                                                 |
| args[2] |  ✅  | 属性名（如 `_Shininess`, `_Cutoff`）                                                                     |
| args[3] |  ✅  | 属性类型，仅识别 `DEFINE` / `TEX_OFFSET` / `TEX_SCALE` / `Color`，**其它任何写法一律按 `SetFloat` 处理** |
| args[4] |  ✅  | 属性值                                                                                                   |

> 与 `useredit`（type=25）走同一个底层写入函数 `TBody.SetMaterialProperty`，区别是本命令不带保存标签、`f_strSaveLoadMpnName` 传 `null`，因此不参与编辑存档。属性类型的四个关键字及其值格式详见 type=25 的说明。

**编辑器命令示例**：

```
material
    wear
    0
    _Shininess
    System.Single
    0.5
```

---

### type=45: uv定義 — UV 定义

```json
{ "type": 45, "args": ["head", "标签名", "0.5:0.3"] }
```

| 参数    | 必需 | 说明                  |
| ------- | :--: | --------------------- |
| args[0] |  ✅  | SlotID                |
| args[1] |  ✅  | 标签名                |
| args[2] |  ✅  | UV 坐标（`U:V` 格式） |

**编辑器命令示例**：

```
uv定義
    head
    タグ名
    0.5:0.3
```

---

### type=46/47/48/66/68/69/70/71: 合成类命令

统一格式：

```json
{"type": 46, "args": ["del"]}
{"type": 46, "args": ["hash", "13341406914984516529"]}
```

| 参数    |  必需  | 说明                           |
| ------- | :----: | ------------------------------ |
| args[0] |   ✅   | `del`（删除）或 `hash`（添加） |
| args[1] | hash时 | ulong 哈希值                   |

| type | 命令名         | 说明 |
| :--: | -------------- | ---- |
|  46  | `ほくろ合成`   | 痣   |
|  47  | `タトゥ合成`   | 纹身 |
|  48  | `ネイル合成`   | 指甲 |
|  66  | `そばかす合成` | 雀斑 |
|  68  | `ひげ合成`     | 胡须 |
|  69  | `しみ合成`     | 斑点 |
|  70  | `しわ合成`     | 皱纹 |
|  71  | `体毛合成`     | 体毛 |

**编辑器命令示例**（添加）：

```
ほくろ合成
    hash
    13341406914984516529
```

**编辑器命令示例**（删除）：

```
ほくろ合成
    del
```

> 其余合成类命令（タトゥ合成/ネイル合成/そばかす合成/ひげ合成/しみ合成/しわ合成/体毛合成）格式相同，只需替换命令名。
> 注意：`hash` 模式运行时要求当前 PropBase 为 SubProp（`MultiMoveTexAdd` / `NailAdd` 内部断言），即这些合成命令在角色以子槽物品方式挂载菜单时才生效。

---

### type=49: gradacolordef — 渐变色定义

```json
{
  "type": 49,
  "args": [
    "hairf",
    "色",
    "crc_hair_f016_grada.tex",
    "0.0~1.0",
    "0.0:0.0~0.0:0.0",
    "H=0,S=100,L=255,C=85~H=228,S=100,L=255,C=85"
  ]
}
```

| 参数     | 必需 | 说明                                                 |
| -------- | :--: | ---------------------------------------------------- |
| args[0]  |  ✅  | SlotID                                               |
| args[1]  |  ✅  | 定义名                                               |
| args[2]  |  ✅  | 范围/纹理（`起始~结束` 或纹理名）                    |
| args[3]  |  ✅  | 渐变比率（如 `0.0~1.0`，浮点比率值）                 |
| args[4]  |  ✅  | 渐变范围（如 `0.0:0.0~0.0:0.0`，`x1:y1~x2:y2` 格式） |
| args[5+] |  ✅  | 颜色数据（HSL 渐变，`~` 分隔多段）                   |

**编辑器命令示例**：

```
gradacolordef
    hairR
    髪色
    hair_ponyr22_grada.tex
    0.0~1.0
    0.0:0.0~0.0:0.0
    H=0,S=100,L=255,C=85~H=228,S=100,L=255,C=85
```

---

### type=50: メイク合成 — 化妆合成

**添加模式参数**：

| 参数    | 必需 | 说明                                                                                                            |
| ------- | :--: | --------------------------------------------------------------------------------------------------------------- |
| args[0] |  ✅  | SlotID                                                                                                          |
| args[1] |  ✅  | 材质编号                                                                                                        |
| args[2] |  ✅  | UV 属性名（如 `_MainTex`）                                                                                      |
| args[3] |  ✅  | 层编号（整数）                                                                                                  |
| args[4] |  ✅  | 纹理文件名                                                                                                      |
| args[5] |  ✅  | 混合模式（`GameUtility.SystemMaterial`，如 `Alpha`, `Multiply`）                                                |
| args[6] |  ✅  | COLOR_TYPE（`InfinityColorTexMgr2.InfColData.COLOR_TYPE`：`NONE` / `INF_COLOR` / `PART_COLOR` / `GRADA_COLOR`） |
| args[7] |  ✅  | UV 坐标（`U:V` 格式）                                                                                           |
| args[8] | 可选 | 颜色数据（HSL 格式，如 `H=0,S=0,L=240,...`；仅当 args[6] 为 `INF_COLOR` 时生效，其他 COLOR_TYPE 时忽略）        |

> 此命令的保存层 tag 由游戏自动生成为 `メイク<MPN编号><子槽号>`，不由参数指定。

```json
{
  "type": 50,
  "args": [
    "face",
    "0",
    "_MainTex",
    "1",
    "make.tex",
    "Alpha",
    "INF_COLOR",
    "0.5:0.5",
    "H=0,S=0,L=240,C=100"
  ]
}
```

**删除模式参数**：

| 参数    | 必需 | 说明       |
| ------- | :--: | ---------- |
| args[0] |  ✅  | `del`      |
| args[1] |  ✅  | SlotID     |
| args[2] |  ✅  | 材质编号   |
| args[3] |  ✅  | UV 属性名  |
| args[4] |  ✅  | ID（整数） |

```json
{ "type": 50, "args": ["del", "head", "5", "_MainTex", "2000"] }
```

**编辑器命令示例**（添加模式）：

```
メイク合成
    face
    0
    _MainTex
    1
    make.tex
    Alpha
    INF_COLOR
    0.5:0.5
    H=0,S=0,L=240,C=100
```

**编辑器命令示例**（删除模式）：

```
メイク合成
    del
    head
    5
    _MainTex
    2000
```

---

### type=51/52/53: ifdef / elseifdef / endifdef — 条件执行

```json
{"type": 51, "args": ["mpn", "==", "wear"]}
{"type": 51, "args": ["isplugin", "in", "PluginName"]}
{"type": 51, "args": ["COLOR_MUGEN"]}
{"type": 52, "args": ["mpn", "==", "mizugi"]}
{"type": 53, "args": []}
```

**ifdef 条件类型**：

- `mpn == <MPN>`: 父 MPN 匹配
- `isplugin in <名称>`: 插件启用检查
- `ismanhead == true/false`: 头部身体类型
- `ismanbody == true/false`: 身体类型
- `<DEFINE枚举>`: 菜单 define 标记检查

> 注意：`mpn ==` 的 MPN 解析与 `<DEFINE枚举>` 检查走 `Enum.TryParse`（**大小写敏感**），与其它命令使用的 `Parse.TryParse`（大小写不敏感）不同，值必须与枚举定义完全一致（如 `wear`、`COLOR_MUGEN`）。另外条件关键字 `mpn` 本身也是大小写敏感的字符串相等（必须全小写）；`isplugin` / `ismanhead` / `ismanbody` 则是不区分大小写的比较。

**编辑器命令示例**：

```
ifdef
    mpn
    ==
    wear

elseifdef
    mpn
    ==
    mizugi

endifdef
```

---

### type=54: mugencolordef — 无限色定义

```json
{
  "type": 54,
  "args": [
    "mizugi",
    "ColorA",
    "H=0,S=0,L=240,C=100,SH=0,SS=0,SL=240,SC=100,T=100"
  ]
}
```

| 参数    | 必需 | 说明                           |
| ------- | :--: | ------------------------------ |
| args[0] |  ✅  | SlotID（如 `mizugi`, `hairf`） |
| args[1] |  ✅  | 层名称                         |
| args[2] |  ✅  | HSL 颜色数据                   |

**编辑器命令示例**：

```
mugencolordef
    mizugi
    ColorA
    H=0,S=0,L=240,C=100,SH=0,SS=0,SL=240,SC=100,T=100
```

---

### type=55: partcolorrgb — 部件颜色(RGB)

```json
{
  "type": 55,
  "args": [
    "accHead",
    "0",
    "_MainTex",
    "crc_acchead003_1.tex",
    "crc_acchead003_1_id1.tex",
    "ColorA",
    "ColorB",
    "ColorC"
  ]
}
```

> 注意：仅 `args[4]` 通过 `&` 拆分为 ID 纹理数组（`_` 表示空位）；`args[5+]` 逐个按 `:` 拆分处理（提取颜色名和可选的 alpha 类型）。`id_tex_is_rgb=true` 时 ID 纹理参与 RGB 通道颜色查找。

| 参数     | 必需 | 说明                                                          |
| -------- | :--: | ------------------------------------------------------------- |
| args[0]  |  ✅  | SlotID                                                        |
| args[1]  |  ✅  | 材质编号                                                      |
| args[2]  |  ✅  | UV 属性名                                                     |
| args[3]  |  ✅  | 纹理文件名（主纹理）                                          |
| args[4]  |  ✅  | ID 纹理文件名（用于 RGB 通道颜色查找，也作为颜色 ID 之一）    |
| args[5+] |  ✅  | 颜色名列表（引用 `partcolordef` 定义，如 `ColorA`, `ColorB`） |

**编辑器命令示例**：

```
partcolorrgb
    accHead
    0
    _MainTex
    crc_acchead003_1.tex
    crc_acchead003_1_id1.tex
    ColorA
    ColorB
    ColorC
```

---

### type=56: meshmorph — 网格变形

```json
{ "type": 56, "args": ["パンツ", "PantsB3", "def=100"] }
```

| 参数    | 必需 | 说明                                                                  |
| ------- | :--: | --------------------------------------------------------------------- |
| args[0] |  ✅  | Tag（`TMorphSkin.BaseBlendValue.Tag`，目前仅 `パンツ` / `靴下` 有效） |
| args[1] |  ✅  | 变形名                                                                |
| args[2] |  ✅  | `def=<百分比整数>`（如 `def=100` = 1.0）                              |

> 作用槽位取自最近一次执行到的 `additem` 的 SlotID（本菜单无 `additem` 时为菜单的 category）。

**编辑器命令示例**：

```
meshmorph
    パンツ
    PantsB3
    def=100
```

---

### type=57: マテリアル参照 — 材质引用

```json
{ "type": 57, "args": ["accUde_2", "0", "accUde", "0"] }
```

| 参数    | 必需 | 说明                              |
| ------- | :--: | --------------------------------- |
| args[0] |  ✅  | 接收方 SlotID（材质被替换的一方） |
| args[1] |  ✅  | 接收方材质编号                    |
| args[2] |  ✅  | 提供方 SlotID（材质的来源）       |
| args[3] |  ✅  | 提供方材质编号                    |

> 注意：方向是 args[0] ← args[2]。源码签名为 `ShareMaterial(slotNameTo=args[0], subPropNo, matNoTo=args[1], slotNameFrom=args[2], matNoFrom=args[3])`（`TBody.cs:2097`），即前两个参数是被改写的一方、后两个才是来源。上例表示 `accUde_2` 借用 `accUde` 的材质实例，从而颜色与纹理自动联动。

**编辑器命令示例**：

```
マテリアル参照
    accUde_2
    0
    accUde
    0
```

---

### type=58: addbonemorph — 添加骨骼变形

```json
{
  "type": 58,
  "args": [
    "wear",
    "裾",
    "Spine0aDressScale",
    "Scl=1.0,1.0,1.0~1.0,1.25,1.25",
    "SpineDressScale",
    "Scl=1.0,1.0,1.0~1.0,1.5,1.5"
  ]
}
```

| 参数     | 必需 | 说明                                                              |
| -------- | :--: | ----------------------------------------------------------------- |
| args[0]  |  ✅  | SlotID（目前仅 `wear`）                                           |
| args[1]  |  ✅  | 变形名（源码硬断言仅支持 `裾`）                                   |
| args[2]  |  ✅  | 骨骼名 1                                                          |
| args[3]  |  ✅  | 骨骼 1 的变换参数（格式 `pos=..~..` / `rot=..~..` / `scl=..~..`） |
| args[4]  | 可选 | 骨骼名 2                                                          |
| args[5]  | 可选 | 骨骼 2 的变换参数                                                 |
| args[4+] | 可选 | 可继续添加更多骨骼名+变换参数对                                   |

变换参数格式：`pos=向量1~向量2` / `rot=向量1~向量2` / `scl=向量1~向量2`

**编辑器命令示例**：

```
addbonemorph
    wear
    裾
    Spine0aDressScale
    Scl=1.0,1.0,1.0~1.0,1.25,1.25
    SpineDressScale
    Scl=1.0,1.0,1.0~1.0,1.5,1.5
```

---

### type=59: 乳首 — 乳头状态

```json
{ "type": 59, "args": ["状態"] }
```

| 参数    | 必需 | 说明                                                 |
| ------- | :--: | ---------------------------------------------------- |
| args[0] |  ✅  | CHIKUBI_STATE 枚举值（`None` / `固定凸` / `基本凹`） |

> 作用槽位取自最近一次执行到的 `additem` 的 SlotID（本菜单无 `additem` 时为菜单的 category）。

**编辑器命令示例**：

```
乳首
    状態
```

---

### type=60: adjcutoff — Cutout 调整

```json
{
  "type": 60,
  "args": ["0", "_Cutoff", "0.45:Stkg7", "0.35:Stkg6", "0.25:Stkg5"]
}
```

| 参数     | 必需 | 说明                                              |
| -------- | :--: | ------------------------------------------------- |
| args[0]  |  ✅  | 材质编号（整数）                                  |
| args[1]  |  ✅  | shader 上的**浮点属性名**（真实样本为 `_Cutoff`） |
| args[2+] |  ✅  | `阈值[:形态名]`，按档位依次列出                   |

> 注意：args[1] 是 shader 的 float 属性名而不是纹理名——源码 `SetCutoutMask(prop, matNo, propName, thresholds)` 最终执行 `m_materials[matNo].SetFloat(propName, 阈值)`（`MaterialMgr.cs:1374-1415`）。
> 档位标签是**网格形态名**：切换到某一档时，游戏会把该档标签以 `靴下` Tag 加成 1.0（`TBody.UpdateCutoutMask`，`TBody.cs:1939-1960`），因此常用于丝袜的厚度/长度档。
> 作用槽位取自最近一次执行到的 `additem` 的 SlotID（本菜单无 `additem` 时为菜单的 category）。

**编辑器命令示例**：

```
adjcutoff
    0
    _Cutoff
    0.45:Stkg7
    0.35:Stkg6
    0.25:Stkg5
```

---

### type=61: parthidemove — 部件隐藏/移动

```json
{"type": 61, "args": ["C", "MOVE&HIDE", "TYPE_SLOT_VISIBLE", "accHead"]}
{"type": 61, "args": ["部件名", "HIDE", "TYPE_BONE_WEIGHT", "wear", "hide=true", "center=Spine0a"]}
```

| 参数     | 必需 | 说明                                                                                                    |
| -------- | :--: | ------------------------------------------------------------------------------------------------------- |
| args[0]  |  ✅  | 部件名                                                                                                  |
| args[1]  |  ✅  | 模式（`HIDE` / `MOVE` / `MOVE&HIDE`，`&` 分隔；走 `Enum.Parse` **大小写敏感**，必须大写 `MOVE`/`HIDE`） |
| args[2]  |  ✅  | 类型（`TYPE_SLOT_VISIBLE` / `TYPE_BONE_WEIGHT`）                                                        |
| args[3]  |  ✅  | SlotID                                                                                                  |
| args[4+] | 可选 | `hide=true/false`, `center=<骨骼名>`                                                                    |

**编辑器命令示例**：

```
parthidemove
    C
    MOVE&HIDE
    TYPE_SLOT_VISIBLE
    accHead
```

---

### type=62: 房tex — 发束纹理

```json
{
  "type": 62,
  "args": [
    "hairf",
    "0=髪1",
    "mask.tex",
    "25#10",
    "色=tex_base.tex",
    "1影=tex_s1.tex"
  ]
}
```

| 参数     | 必需 | 说明                                 |
| -------- | :--: | ------------------------------------ |
| args[0]  |  ✅  | SlotID                               |
| args[1]  |  ✅  | `编号=标签名`                        |
| args[2]  |  ✅  | 遮罩纹理名                           |
| args[3]  |  ✅  | `中心值#宽度值`（整数 0-255 或浮点） |
| args[4+] |  ✅  | `标签名=纹理名`（颜色定义）          |

> 注意：同一 SlotID 的多个 `房tex` 命令会共享渲染目标（`shareRtTargetPart`）。第二次及以后对同一 SlotID 的 `房tex` 调用会自动将 `shareRtTargetPart` 设为首次调用时的标签名，因此命令顺序会影响结果。另外运行时要求 `propBase` 非空（会写 `propBase.subPropIsFusaTex`），正常由发束子槽（SubProp）执行时满足该要求。

**编辑器命令示例**：

```
房tex
    hairf
    0=髪1
    mask.tex
    25#10
    色=tex_base.tex
    1影=tex_s1.tex
```

---

### type=63: munekagergb — 胸阴影(RGB)

```json
{
  "type": 63,
  "args": [
    "onepiece",
    "0",
    "_MainTex",
    "crc_dress027_onep_1_munekage.tex",
    "Alpha",
    "crc_dress027_onep_1_id1.tex&crc_dress027_onep_1_id2.tex"
  ]
}
```

| 参数    | 必需 | 说明                         |
| ------- | :--: | ---------------------------- |
| args[0] |  ✅  | SlotID                       |
| args[1] |  ✅  | 材质编号                     |
| args[2] |  ✅  | UV 属性名                    |
| args[3] |  ✅  | 阴影纹理名                   |
| args[4] |  ✅  | 混合模式                     |
| args[5] |  ✅  | RGB 纹理 ID 列表（`&` 分隔） |

**编辑器命令示例**：

```
munekagergb
    onepiece
    0
    _MainTex
    shadow.tex
    Alpha
    id1.tex&id2.tex
```

---

### type=64: mask消去 — 遮罩删除

```json
{ "type": 64, "args": ["onepiece", "mask.tex"] }
```

| 参数    | 必需 | 说明       |
| ------- | :--: | ---------- |
| args[0] |  ✅  | SlotID     |
| args[1] |  ✅  | 遮罩纹理名 |

**编辑器命令示例**：

```
mask消去
    onepiece
    mask.tex
```

---

### type=65: munekage — 胸阴影

```json
{ "type": 65, "args": ["bra", "0", "_MainTex", "shadow.tex", "Alpha"] }
```

参数与 `munekagergb` 类似但无 RGB 纹理列表。

**编辑器命令示例**：

```
munekage
    bra
    0
    _MainTex
    shadow.tex
    Alpha
```

---

### type=67: ちんこ — 男性器官状态

```json
{ "type": 67, "args": ["状態"] }
```

| 参数    | 必需 | 说明                                     |
| ------- | :--: | ---------------------------------------- |
| args[0] |  ✅  | CHINKO_STATE 枚举值（`None` / `しまう`） |

> 作用槽位取自最近一次执行到的 `additem` 的 SlotID（本菜单无 `additem` 时为菜单的 category）。

**编辑器命令示例**：

```
ちんこ
    状態
```

---

### type=72: cutout消去 — Cutout 删除

```json
{ "type": 72, "args": ["SlotID", "纹理名"] }
```

| 参数    | 必需 | 说明          |
| ------- | :--: | ------------- |
| args[0] |  ✅  | SlotID        |
| args[1] |  ✅  | Cutout 纹理名 |

**编辑器命令示例**：

```
cutout消去
    wear
    cutout.tex
```

---

### type=73: タッチ範囲tex — 触摸范围纹理

```json
{ "type": 73, "args": ["SlotID", "材质编号", "纹理名"] }
```

| 参数    | 必需 | 说明          |
| ------- | :--: | ------------- |
| args[0] |  ✅  | SlotID        |
| args[1] |  ✅  | 材质编号      |
| args[2] |  ✅  | ID 纹理文件名 |

> 注意：args[2] 是**触摸区域 ID 纹理**——源码走 `MaterialMgr.SetEditTouchAreaTex(matNo, fileName)` → `EditTouchAreaMgr.SetIdTex(fileName)`（`MaterialMgr.cs:276-284`），决定编辑界面点击模型时命中哪个部位。对同一材质重复设置时，先前的 ID 纹理会被释放。

**编辑器命令示例**：

```
タッチ範囲tex
    body
    0
    touch_range_id.tex
```

---

## 四、常用枚举值参考

### TBody.SlotID（槽位标识）

```
public enum SlotID
{
    none = -1,
    body,
    head,
    eye,
    hairF,
    hairR,
    hairS,
    hairS_2,
    hairT,
    hairT_2,
    wear,
    skirt,
    onepiece,
    mizugi,
    mizugi_top,
    mizugi_buttom,
    panz,
    slip,
    bra,
    stkg,
    shoes,
    headset,
    glove,
    jacket,
    vest,
    shirt,
    accHead,
    accHead_2,
    hairAho,
    accHana,
    accHa,
    accKami_1_,
    accMiMiR,
    accKamiSubR,
    accNipR,
    HandItemR,
    accKubi,
    accKubiwa,
    accHeso,
    accUde,
    accUde_2,
    accAshi,
    accAshi_2,
    accSenaka,
    accShippo,
    accKoshi,
    accAnl,
    accVag,
    kubiwa,
    megane,
    accXXX,
    chinko,
    chikubi,
    accFace,
    accHat,
    accHat_2,
    kousoku_upper,
    kousoku_lower,
    seieki_naka,
    seieki_hara,
    seieki_face,
    seieki_mune,
    seieki_hip,
    seieki_ude,
    seieki_ashi,
    accNipL,
    accMiMiL,
    accKamiSubL,
    accKami_2_,
    accKami_3_,
    HandItemL,
    underhair,
    asshair,
    moza,
    end,
    accAcc1,
    accAcc2,
    accAcc3,
    accAcc4,
    accAcc5,
    accAcc6,
    accAcc7,
    accAcc8,
    accAcc9,
    accAcc10,
    accAcc11,
    accAcc12,
    accAcc13,
    accAcc14,
    accAcc15,
    accAcc16,
    accAcc17,
    accAcc18,
    accAcc19,
    accAcc20,
    accAcc21,
    accAcc22,
    accAcc23,
    accAcc24,
    accAcc25,
    accAcc26,
    accAcc27,
    accAcc28,
    accAcc29,
    accAcc30,
    accAcc31,
    accAcc32,
    accAcc33,
    accAcc34,
    accAcc35,
    accAcc36,
    accAcc37,
    accAcc38,
    accAcc39,
    accAcc40,
    accAcc41,
    accAcc42,
    accAcc43,
    accAcc44,
    accAcc45,
    accAcc46,
    accAcc47,
    accAcc48,
    accAcc49,
    accAcc50,
    accAcc51,
    accAcc52,
    accAcc53,
    accAcc54,
    accAcc55,
    accAcc56,
    accAcc57,
    accAcc58,
    accAcc59,
    accAcc60,
    accAcc61,
    accAcc62,
    accAcc63,
    accAcc64,
    accAcc65,
    accAcc66,
    accAcc67,
    accAcc68,
    accAcc69,
    accAcc70,
    accAcc71,
    accAcc72
}
```

### GameUtility.SystemMaterial（混合模式）

```
public enum SystemMaterial
{
    Alpha,
    BlendSelf,
    Multiply,
    InfinityColor,
    InfinityColorPart,
    InfinityColorGrada,
    TexTo8bitTex,
    AddNormal,
    AlphaDstAlpha,
    Screen,
    Max
}
```

> 旧版 COM3D2 中的 `Mul`/`Add`/`Sub`/`Min` 在 KCES 中不存在，请勿混用。

### MaidInfinityColor.PARTS_COLOR（部件颜色类型）

```
public enum PARTS_COLOR
{
    NONE = -1,
    HAIR,
    EYE_BROW,
    UNDER_HAIR,
    ASS_HAIR,
    SKIN,
    HAIR_OUTLINE,
    SKIN_OUTLINE,
    EYE_WHITE,
    HOKURO,
    TATOO,
    SOBAKASU,
    MATSUGE_UP,
    MATSUGE_LOW,
    FUTAE,
    PART_COLOR,
    GRADA_COLOR,
    MAKE,
    MUGEN_COLOR,
    HIGE,
    SHIMI,
    SHIWA,
    BODY_HAIR,
    MAX
}
```

（menu 命令中常用：NONE / MUGEN_COLOR / GRADA_COLOR / PART_COLOR / MAKE）

### MaterialMgr.ALPHA_TYPE（Alpha 混合类型）

```
ALPHA_NONE, ALPHA_TEX, ALPHA_MAT
```

### MPN（装备分类）

    ```

public enum MPN
{
null_mpn,
Hara,
KubiScl,
UdeScl,
DouPer,
sintyou,
kata,
MuneL,
MuneS,
MuneM,
MuneUpDown,
MuneYori,
MuneYawaraka,
MunePosX,
MunePosY,
MuneThick,
MuneLong,
MuneDir,
DouThick1X,
DouThick1Y,
DouThick2X,
DouThick2Y,
DouThick3X,
DouThick3Y,
ShoulderThick,
UpperArmThickX,
UpperArmThickY,
LowerArmThickX,
LowerArmThickY,
ElbowThickX,
ElbowThickY,
NeckThickX,
NeckThickY,
HandSize,
DouThick4X,
DouThick4Y,
DouThick5X,
DouThick5Y,
WaistPos,
HipSize,
HipRot,
ThighThickX,
ThighThickY,
KneeThickX,
KneeThickY,
CalfThickX,
CalfThickY,
AnkleThickX,
AnkleThickY,
FootSize,
UpperArmLowerThickX,
UpperArmLowerThickY,
WristThickX,
WristThickY,
ClavicleThick,
ShoulderTension,
ThighLowerThickX,
ThighLowerThickY,
ThighShin,
HaraN,
ChikubiH,
ChikubiK1,
ChikubiK2,
ChikubiK2_MuneS,
ChikubiR,
ChikubiW,
Nyurin1,
Nyurin2,
Nyurin3,
Nyurin4,
Nyurin5,
Nyurin6,
Nyurin7,
Nyurin8,
ChikubiWearTotsu,
NyurinScale,
FatUpper,
FatUnder,
MuscleSkin,
HipYawaraka,
HaraYawaraka,
MuneSpringPower,
MuneSpringMove,
HaraSpringPower,
HaraSpringMove,
HipSpringPower,
HipSpringMove,
HeadX,
HeadY,
FaceShape,
FaceShapeSlim,
EyeSclX,
EyeSclY,
EyePosX,
EyePosY,
EyePosX_2,
EyePosY_2,
EyeClose,
EyeBallPosY,
EyeBallSclX,
EyeBallSclY,
EarNone,
EarElf,
EarRot,
EarScl,
NosePos,
NoseScl,
MayuShapeIn,
MayuShapeOut,
MayuX,
MayuY,
MayuY_2,
MayuRot,
MayuThick,
MayuLong,
Yorime,
MabutaUpIn,
MabutaUpIn2,
MabutaUpMiddle,
MabutaUpOut,
MabutaUpOut2,
MabutaLowIn,
MabutaLowMiddle,
MabutaLowOut,
Eyedel,
Itome,
Ha1,
Ha2,
Ha3,
Ha4,
Ha5,
Ha6,
FutaePosX,
FutaePosY,
FutaeRot,
HitomiHiPosX,
HitomiHiPosY,
HitomiHiSclY,
HitomiShapeUp,
HitomiShapeLow,
HitomiShapeIn,
HitomiShapeOutUp,
HitomiShapeOutLow,
HitomiRot,
HohoShape,
LipThick,
WearSuso,
WearMuneShadowRate,
KuikomiPants,
KuikomiStkg,
CheekRate,
FaceglossRate,
MayuRate,
EyeShadowRate,
EyeHiRateL,
EyeHiRateR,
LipRate,
LipTsuyaRate,
NailTsuyaRate,
SkinHiyakeRate,
ArmpitHairRate,
UnderHairRate,
AssHairRate,
StkgRate,
LipShadowRate,
Hanasuji,
Washibana,
EyeDel_shadowRate,
Nose_RimlightMask,
Ago_Back_Foward,
Ago_Long_Short,
Ago_Sharp,
AgoHaba_Large_Small,
AgoNiku_Fat_Slim,
AgoSentan_Back_Foward,
AgoSentan_Long_Short,
AgoSentan_Sharp,
AgoSentanHaba_Large_Small,
AgoSide_Back_Foward,
Cheekbone_Sharp,
Cheekbone_Slim_Fat,
Era_Sharp,
EyePosZ,
Face_Slim,
Face_UnderBack_Foward,
Face_UnderLarge_Small,
Ho_UnderBack_Foward,
Ho_UpperBack_Foward,
Ho_Sharp,
Ho_Down_Up,
Ho_Hukurami,
Hanasuji_Back_Foward,
NoseSentan_Marumi,
NoseSentan_Sharp,
Nose_Shape,
body,
moza,
head,
hairf,
hairr,
hairt,
hairs,
hairaho,
haircolor,
skin,
skin_nikukan,
skin_hiyake,
acctatoo,
accnail,
underhair,
asshair,
armpithair,
hokuro,
mayu,
lip,
lip_tsuya,
chikubi,
nyurin,
eye,
eye_r,
eye_hi,
eye_hi_r,
eyewhite,
eyewhite_r,
nose,
facegloss,
matsuge_up,
matsuge_low,
futae,
hoho_some,
eye_shadow,
cheek,
EyeDel_shadow,
nail_hi,
kuchi_naka,
sobakasu,
hige,
shiwa,
shimiibo,
bodyhair,
wear,
skirt,
mizugi,
mizugi_top,
mizugi_buttom,
bra,
panz,
slip,
stkg,
shoes,
headset,
glove,
acchead,
accha,
acchana,
accface,
acckamisub,
acckami,
accmimi,
accnip,
acckubi,
acckubiwa,
accheso,
accude,
accashi,
accsenaka,
accshippo,
acckoshi,
accanl,
accvag,
megane,
accxxx,
handitem,
acchat,
onepiece,
outerwear,
jacket,
vest,
shirt,
accAcc1,
accAcc2,
accAcc3,
accAcc4,
accAcc5,
accAcc6,
accAcc7,
accAcc8,
accAcc9,
accAcc10,
accAcc11,
accAcc12,
accAcc13,
accAcc14,
accAcc15,
accAcc16,
accAcc17,
accAcc18,
accAcc19,
accAcc20,
accAcc21,
accAcc22,
accAcc23,
accAcc24,
set_maidwear,
set_mywear,
set_underwear,
set_body,
set_face,
folder_eye,
folder_mayu,
folder_underhair,
folder_asshair,
folder_skin,
folder_eyewhite,
folder_chikubi,
folder_nyurin,
folder_matsuge_up,
folder_matsuge_low,
folder_futae,
folder_lip,
folder_cheek,
folder_eye_shadow,
NyurinSelect,
kousoku_upper,
kousoku_lower,
seieki_naka,
seieki_hara,
seieki_face,
seieki_mune,
seieki_hip,
seieki_ude,
seieki_ashi
}

```

### Menu.DEFINE（定义标记）

```

NONE=0, COLOR_MAMA=1, COLOR_MUGEN=2, COLOR_BUBUN=4, COLOR_GRADA=8

```

Flags 枚举，可组合。

### Menu.TargetBodyType（身体类型）

```

None=0, Woman=1, Man=2

```

### Menu.Attribute（属性标记）

```

None=0, WomanReccomend=1, ManReccomend=2, ManSuits=4, NoExpressionFace=8, NoMoveTatooHokuro=16

```

Flags 枚举。

### TBody.MOVE_HIDE_MODE（移动隐藏模式）

```

NONE=0, MOVE=1, HIDE=2

```

Flags 枚举，可组合（如 `MOVE&HIDE`）。

### TBody.PART_HIDE_TYPE（部件隐藏类型）

```

TYPE_BONE_WEIGHT, TYPE_SLOT_VISIBLE

```

### TMorphSkin.BaseBlendValue.Tag（meshmorph 变形标签）

```

パンツ, 靴下, MAX

```

> meshmorph 的 args[0] 目前只有 `パンツ`/`靴下` 两个有效值。

### Menu.HaraYureLimitType（腹部摇摆）

```

None=0, YureAvailable=1, YureDisable=2

```

---

## 五、Menu 对象结构参考（KCES MessagePack JSON）

```json
{
  "version": 1005,
  "guid": 15539618002080322521,
  "id": 41100326004452930,
  "fileName": "cm3d2_skinhi008.menu",
  "itemName": "COM3D2_スリーズ",
  "iconFileName": "_i_skinhi008",
  "infoText": "点々とあるハイライト。",
  "priority": 100,
  "parentId": 0,
  "isMan": false,
  "isDiff": false,
  "isDelete": false,
  "commandList": [
    { "type": 42, "args": ["head", "目ハイ色", "H=0,S=100,L=255,C=85"] },
    {
      "type": 24,
      "args": [
        "head",
        "eye_hi=2&eye_hi_r=7",
        "_MainTex",
        "skinhi008.tex",
        "MUGEN_COLOR",
        "mugen",
        "目ハイ色"
      ]
    }
  ],
  "categoryText": "eye_hi",
  "colorSetText": "null_mpn",
  "defineTagNames": 2,
  "preMulTexDatas": null,
  "colvariFileNameExp": null,
  "colvariInfo": null,
  "srcFileHashCRC32": 893156666,
  "defineFirst": 2,
  "partsVer": null,
  "isRecommendMan": false,
  "targetBodyType": 0,
  "attribute": 0,
  "hideInEdit": false,
  "toeLockSlotId": null,
  "exportModelFormTextureName": null,
  "isHarayureAvailable": 0,
  "skirt_phys": 0,
  "hairMake": null
}
```

> 注：`version` 当前为 1005（`Menu.FixVersion`）；低于 1003 的旧数据反序列化时会经 `ConvertToCRES2Format` 补充 `targetBodyType`/`attribute`。`categoryText`/`colorSetText` 仅在序列化阶段存在（`OnBeforeSerialize` 写入），反序列化后被清空并还原为 `category`/`colorSet`。JSON 中的 `type` 为 `Menu.Command.Type` 枚举的整数值。

---

## 六、参数格式速查

| 格式     | 示例                                                       | 说明                 |
| -------- | ---------------------------------------------------------- | -------------------- |
| SlotID   | `wear`, `hairF`, `accHead`                                 | 部件槽位名           |
| MPN      | `wear`, `mizugi`, `accAcc1`                                | 装备分类             |
| 整数     | `0`, `100`, `255`                                          | 编号/阈值            |
| 浮点数   | `0.5`, `1.0`, `0.0004`                                     | 比例/坐标            |
| 向量     | `x y z` (三个独立参数)                                     | 3D 坐标              |
| 颜色     | `R G B A` (各 0-255)                                       | RGBA 颜色            |
| HSL      | `H=160,S=160,L=256,C=101,SH=156,SS=64,SL=200,SC=100,T=221` | HSL 颜色             |
| 文件名   | `tex.tex`, `model.model`, `mate.mate`                      | 资源文件名           |
| 键值对   | `key=value`                                                | 可选参数             |
| 多选     | `A&B`                                                      | 多个值用 `&` 连接    |
| 范围     | `0.0~1.0`                                                  | 范围表示（`~` 分隔） |
| 条件     | `MPN=值&MPN=值`                                            | MPN 条件选择         |
| 组合模式 | `MOVE&HIDE`                                                | Flags 组合           |
| UV       | `0.5:0.3`                                                  | UV 坐标（`U:V`）     |
| 遮罩     | `中心#宽度`                                                | 遮罩范围             |
