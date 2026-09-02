# KCES 物理文件格式速查

本文是 KCES / KCES2 / COM3D2.5 里那一堆物理配置文件的格式参考，配合《第 7 课，在 KCES2 中使用 Magica Cloth 2 让物体有物理效果》阅读。

信息来自游戏代码（COM3D2 2.49.x、COM3D2.5 3.49.x、KCES 1.34.5、KCES2 1.35.1 / 1.36.0）和 [MeidoSerialization](https://github.com/MeidoPromotionAssociation/MeidoSerialization) 的实现，随版本可能变化。

编辑这些文件可以使用 [KCES MOD EDITOR](https://github.com/MeidoPromotionAssociation/KCES_MOD_EDITOR)（仍在开发中）。

<br>

## 一、四代物理系统对照

KCES 时代最容易搞混的就是"到底有几套物理"。答案是四套，而且它们**同时存在于代码里**。

| 世代 | 物理实现 | 参数文件 | 碰撞器文件 |
|---|---|---|---|
| CM3D2 | 自研 `TBoneHair_`（无参数文件，按骨骼名硬编码） | — | — |
| COM3D2 | [Dynamic Bone](https://assetstore.unity.com/packages/tools/animation/dynamic-bone-16743) 插件 | `.phy`、裙子 `.psk` | `.col` |
| KCES 1 早期 | 自研 `DynamicYureBone`（头发/挂件）<br>[Magica Cloth **v1**](https://assetstore.unity.com/packages/tools/physics/magica-cloth-136853)（裙子/袖子） | 头发挂件 `.dbconf`<br>裙子 `.dsbconf`<br>袖子 `.dslconf` | `_col.dbcol`<br>`_col.dslcol` |
| KCES 1 后期 / KCES 2 | [**Magica Cloth 2**](https://assetstore.unity.com/packages/tools/physics/magica-cloth-2-242307) | 头发挂件 `.db2conf`<br>裙子 `.dsb2conf`<br>袖子 `.dsl2conf` | 复用 `_col.dbcol` / `_col.dslcol` |

> **注意**：Magica Cloth 2 **不是 KCES2 才有的**。KCES 1.34.5 里 `MagicaClothV2` 程序集和三个 `*2conf` 扩展名就已经齐全了。KCES1 和 KCES2 的区别在**启用范围**：KCES1 只在 `crc2_` / `ver ≥ 300` / `skirt_phys 2` 等条件下才建 Magica Cloth 2，而且挂件完全不建；KCES2 对头发、裙子、袖子、挂件**无条件全建**，并新增了 `Stop` / `Simple` / `Full` 画质档位切换。文件格式和骨骼命名规则两代通用。

几个要点：

- **KCES1 用的是 Magica Cloth 一代**，只用在裙子和袖子上，参数是 MessagePack 序列化的 `MagicaCloth.ClothParams`（`bendDistanceStiffness`、`clampPositionLength` 这类字段名）。
- **KCES 后期版本以及 KCES2 换成了 Magica Cloth 2**，参数是 Unity JSON 序列化的 `MagicaCloth2.ClothSerializeData`（`distanceConstraint`、`angleLimitConstraint` 这类字段名）。
- 老的文件**没有被删掉**。KCES2 装备物品时会把 `.dbconf`（一代自研摇曳骨）和 `.db2conf`（Magica Cloth 2）**都读进来**，运行时按画质设置切换谁来驱动骨骼（见第五节）。
- COM3D2.5 里 COM3D2 的 `.phy` / `.col` / `.psk` 体系完全保留，只在 CRC（KCES）身体上才走 KCES 那套。

<br>

## 二、统一的二进制封套

KCES 自己的物理文件（不管哪一代）都是同一个壳：

```
+0   int32   payloadLength      // 小端
+4   byte[]  payload            // MessagePack，通常带 LZ4 Block Array 压缩
```

`payload` 是 MessagePack-CSharp 用 `MessagePackCompression.Lz4BlockArray` 序列化的结果，也就是外面套了一层 **MessagePack extension type 98**（`Lz4Block` 是 99，见 `MessagePack/ThisLibraryExtensionTypeCodes.cs`）。

payload 内部的类型按扩展名分派：

| 扩展名                    | payload 类型                                          | MeidoSerialization 的 kind |
|---------------------------|-------------------------------------------------------|----------------------------|
| `.dbconf`                 | `DynamicBoneStatus`（indexed map，Key 0–15）          | `dynamic-bone-status`      |
| `_col.dbcol`              | `DynamicBoneColliderData`                             | `collider-package`         |
| `.dsbconf`                | `MagicaCloth.ClothParams`（一代）                     | `cloth-params`             |
| `.dslconf`                | `MagicaCloth.ClothParams`（一代）                     | `cloth-params`             |
| `_col.dslcol`             | `DynamicBoneColliderData`                             | `collider-package`         |
| **`.db2conf`**            | **MessagePack string，内容是 Magica Cloth 2 的 JSON** | `msgpack-json-string`      |
| **`.dsb2conf`**           | 同上                                                  | `msgpack-json-string`      |
| **`.dsl2conf`**           | 同上                                                  | `msgpack-json-string`      |
| `.ikcol` / `.ikcol.bytes` | `IKColliderPackage`                                   | `ik-collider-package`      |
| `.limbcol`                | `LimbColliderPackage`                                 | `limb-collider-package`    |

也就是说 Magica Cloth 2 那三个文件是**双层**结构：

```
int32 长度 → MessagePack(LZ4) → 一个 MessagePack 字符串 → 字符串本身是 JSON → ClothSerializeData
```

<br>

## 三、⚠ 同名不同格式：KCES 原生 vs ExportCM 旁车

KCES2 有一个"导出角色到 COM3D2"的功能（代码里是 `ExportCM.ExportMaidToCOM3D2`）。它吐出来的文件**扩展名和 KCES 自己的一样，但格式不一样**，因为要给 COM3D2.5 读。

| 扩展名 | KCES 内部（`GameResource` 读） | ExportCM 导出的（COM3D2.5 `GameUty` 读） |
|---|---|---|
| `.dbconf` | int32 + MessagePack `DynamicBoneStatus` | **纯 JSON 文本**（`JsonUtility.ToJson`） |
| `_col.dbcol` | int32 + MessagePack `DynamicBoneColliderData` | **纯 JSON 文本** |
| `_col.dslcol` | int32 + MessagePack `DynamicBoneColliderData` | **`BinaryWriter.Write(string)` 的 7 位变长长度前缀 + UTF8 JSON** |
| `.db2conf` / `.dsb2conf` / `.dsl2conf` | int32 + MessagePack string(JSON) | 相同 |

MeidoSerialization 把这个区别建模成 `storageVariant`：

- `int32-length-lz4-messagepack` —— KCES 原生
- `exportcm-unity-json` —— ExportCM 的纯 JSON（`.dbconf` / `.dbcol`）
- `exportcm-dotnet-string-unity-json` —— ExportCM 的 .NET 字符串前缀 JSON（`.dslcol`）

拿到一个 `.dbconf` 先看前几个字节：如果是 `{` 就是 ExportCM 的 JSON 版，如果是一个小的 int32 就是 KCES 原生版。

> KCES2 自己导出 `.dslcol` 用的是 `.NET 字符串 + JSON`，但 KCES2 自己的加载器只认 MessagePack —— 也就是说**KCES2 导出的 `.dslcol` 放回 KCES2 里是读不了的**，它只给 COM3D2.5 用。这不是 bug 而是设计如此（导出功能的目标就是 COM3D2.5）。

<br>

## 四、`.db2conf` / `.dsb2conf` / `.dsl2conf` 里的 JSON

解出来的 JSON 就是 Magica Cloth 2 的 `ClothSerializeData` 经 Unity `JsonUtility.ToJson` 的结果，参数含义直接查 [Magica Cloth 2 官方文档](https://magicasoft.jp/en/magica-cloth-2-2/)，游戏没改语义。

### 4.1 ⭐ 一半以上的字段是无效的

这是本文最重要的一条。游戏加载时调的是 `ClothSerializeData.ImportJson`：

```csharp
var tempBuffer = new TempBuffer(this);      // 先把当前值备份
JsonUtility.FromJsonOverwrite(json, this);  // 用 JSON 覆盖
tempBuffer.Pop(this);                       // 再把备份盖回去！
DataValidate();
```

`TempBuffer` 会在导入后**把下列字段还原成代码设定的值，你在 JSON 里写什么都不生效**：

`clothType`、`sourceRenderers`、`meshWriteMode`、`paintMode`、`paintMaps`、`paintMapUvChannel`、`rootBones`、`connectionMode`、`rotationalInterpolation`、`rootRotation`、`updateMode`、`animationPoseRatio`、`reductionSetting`、`customSkinningSetting`、`normalAlignmentSetting`、`normalAxis`、`colliderCollisionConstraint.colliderList`、`colliderCollisionConstraint.collisionBones`、`selfCollisionConstraint.syncPartner`、`stablizationTimeAfterReset`、`blendWeight`、`cullingSettings`、`inertiaConstraint.anchor`、`inertiaConstraint.anchorInertia`

好处是：官方文件里那一大堆 `{"instanceID":-2507546}` **全是无意义的垃圾数据**，你手写 JSON 时随便填、甚至填空数组都没关系。

### 4.2 真正有效的字段

| 字段 | 含义 | 备注 |
|---|---|---|
| `gravity` | 重力大小 | Clamp 到 0–20（Inspector 里限 0–10） |
| `gravityDirection` | 重力方向 | 会被归一化；长度过小则归零 |
| `gravityFalloff` | 重力沿骨链衰减 | 0–1 |
| `damping` | 阻尼 | 曲线型，内部还会 ×0.2 |
| `radius` | 粒子半径 | Clamp 0.001–1，曲线型 |
| `inertiaConstraint` | 移动/旋转惯性、`depthInertia`、离心力、三个速度上限 | `anchor` / `anchorInertia` 除外 |
| `tetherConstraint.distanceCompression` | 压缩限制 | |
| `distanceConstraint.stiffness` | 距离约束刚度 | 曲线型 |
| `triangleBendingConstraint.stiffness` | 三角弯曲 | 仅 MeshCloth，这里用不到 |
| `angleRestorationConstraint` | 角度复原：`useAngleRestoration` / `stiffness` / `velocityAttenuation` / `gravityFalloff` | 头发挂件主要靠这个"回弹" |
| `angleLimitConstraint` | 角度限制：`useAngleLimit` / `limitAngle` / `stiffness` | 防止骨骼翻折 |
| `motionConstraint` | `useMaxDistance` / `maxDistance` / `useBackstop` / `backstopRadius` / `backstopDistance` / `stiffness` | backstop 是"不许陷进身体"的软约束 |
| `colliderCollisionConstraint.mode` | 碰撞模式：`None` / `Point` / `Edge` / `Surface` | **列表无效，只有模式和摩擦有效** |
| `colliderCollisionConstraint.friction` | 摩擦 | |
| `selfCollisionConstraint` | `selfMode` / `surfaceThickness` / `syncMode` / `clothMass` | |
| `wind` | `influence` / `frequency` / `turbulence` / `blend` / `synchronization` / `depthWeight` / `movingWind` | |
| `springConstraint` | 弹簧约束 | 仅 `BoneSpring` 类型（乳摇等身体部位）用 |

曲线型字段（`CurveSerializeData`）的 JSON 形态是：

```json
{
  "value": 0.02,
  "useCurve": false,
  "curve": {
    "serializedVersion": "2",
    "m_Curve": [
      {"serializedVersion":"3","time":0.0,"value":1.0,"inSlope":0.0,"outSlope":0.0,
       "tangentMode":0,"weightedMode":0,"inWeight":0.0,"outWeight":0.0},
      {"serializedVersion":"3","time":1.0,"value":1.0,"inSlope":0.0,"outSlope":0.0,
       "tangentMode":0,"weightedMode":0,"inWeight":0.0,"outWeight":0.0}
    ],
    "m_PreInfinity": 2, "m_PostInfinity": 2, "m_RotationOrder": 4
  }
}
```

`useCurve = false` 时只用 `value`；`true` 时 `value` 乘以曲线在 [0,1] 上的取值，横轴是"骨骼在骨链上的深度"（0 = 根，1 = 尖端）。

### 4.3 现成模板从哪来

游戏代码里就硬编码了 6 份完整 JSON，抄改最省事：

| 位置 | 内容 |
|---|---|
| `DynamicYureBone.LoadDummyMagica2DynamicYureBoneSetting` | 通用挂件、`hairf`、`hairs`、`hairt`、`hairr` 五份 |
| `DynamicKCES2SkirtBone.LoadDummyMagica2Params` | 裙子一份 |

或者穿一件效果好的官方衣服，用 KCES2 的导出功能导出一次，里面就有对应的 `.db2conf`。

<br>

## 五、`.dbconf`（DynamicBoneStatus）为什么还在

KCES2 装备物品时是这样的（`DynamicBoneMgr.SetUpGameObject`）：

1. `LoadDynamicYureBoneSetting` —— 读 `.dbconf` + `_col.dbcol`，建一代自研摇曳骨
2. `MagicaCloth2*Init` —— 建 Magica Cloth 2 组件
3. `LoadMagica2DynamicYureBoneSetting` —— 读 `.db2conf`
4. `OnSetPhysicType(画质档位)` —— 决定这一刻谁说话

画质档位是 `PhysicsModeData.PhysicsMode`，玩家在设置里按"头发 / 身体 / 服装 / 挂件"分别选：

| 档位 | 行为 |
|---|---|
| `Stop` | Magica Cloth 组件 `enabled = false`，完全不动 |
| `Simple` | 强制走 Magica Cloth 2，并且**把 `colliderCollisionConstraint.mode` 覆写成 `Point`** |
| `Full` | 恢复你文件里的碰撞模式；是否用 Magica Cloth 2 取决于 `IsOriginCRCPhysic` |

所以：

- **调参一定要在 `Full` 档测试**，否则精心调的 `Edge` / `Surface` 碰撞会被降级成 `Point`。
- 两份参数文件（`.dbconf` 和 `.db2conf`）最好都给，不然低画质档位下可能表现异常。

<br>

## 六、碰撞器是怎么拼起来的

Magica Cloth 2 的碰撞器列表在 JSON 里是无效的（见 4.1），实际来源有三处：

```
_col.dbcol  →  LoadCollider()  →  colliderList（NativeXxxCollider）
                                       ↓ SetupMagica2ColliderFromNativeCollider()
                                 MagicaCapsuleCollider / MagicaSphereCollider
                                       +
                          8 个肢体胶囊（limbconf.limbcol 定义）
                                       +
                          1 个地板碰撞器（body.magica2FloorCollider）
```

`_col.dbcol` 里的碰撞器按类型转换：

| `.dbcol` 里的类型 | 转成 Magica Cloth 2 的 |
|---|---|
| `MaidPropCol`（随体型滑条变化的胶囊） | `MagicaCapsuleCollider` |
| `Capsule` | `MagicaCapsuleCollider` |
| `Sphere` | `MagicaSphereCollider` |
| **`Plane`** | **被丢弃** |

> **COM3D2 老技巧失效提醒**：COM3D2 时代常用"平面碰撞器 + 约束边界 Inside"把摇曳骨圈在一个范围内，这招在 `.dbcol` 的 Magica Cloth 2 路径下不生效，因为平面碰撞器不会被转换（它只会被创建成 `NativePlaneCollider`，供一代自研摇曳骨使用）。替代方案是用 `motionConstraint.useMaxDistance` 或 `useBackstop`。袖子的 `.dslcol` 是例外，见 6.2。

8 个肢体碰撞器是全局的，定义在 `limbconf.limbcol` 里，MOD 改不了参数：

`UpperArm_L`、`Forearm_L`、`UpperArm_R`、`Forearm_R`、`Thigh_L`、`Calf_L`、`Thigh_R`、`Calf_R`

`_col.dbcol` 里的 `limbEnableList` 可以逐个开关它们，但**只对一代自研摇曳骨有效** —— `DynamicYureBone` 里 `isEnable` 只在粒子碰撞循环（`LimbColliderInfoList[j].collider.Collide(...)`）中被读取，从来没有被写到 `magica2LimbColliderList` 里那些 `MagicaCapsuleCollider` 的 `enabled` 上。也就是说 **Magica Cloth 2 路径下 8 个肢体碰撞器恒定全开**。袖子的 `.dslcol` 又是例外。

裙子（`DynamicKCES2SkirtBone`）不读任何碰撞器文件，它的碰撞器全部由代码在 `Bip01 Spine` / `Bip01 Pelvis` / `Bip01 L·R Thigh` / `Bip01 L·R Calf` / `Hip_L` / `Hip_R` 上硬编码生成，碰撞模式固定 `Edge`。所以**裙子的碰撞器完全无法自定义**。

### 6.1 `.dbcol` 和 `.dslcol` 的关系

两者的 **payload 类型完全相同**，都是 `DynamicBoneColliderData`（MeidoSerialization 里同一个 `collider-package` kind），封套也一样是 `int32 长度 + MessagePack(LZ4)`。也就是说**用同一套编解码器就能读写两者**，字段结构没有任何差别。

区别全在"谁读它、读完怎么用"：

| | `_col.dbcol` | `_col.dslcol` |
|---|---|---|
| 使用者 | `DynamicYureBone`（头发 / 挂件） | `DynamicSleeveBone`（袖子） |
| 兜底文件名 | `default_<mpn>_col.dbcol` → `default_yure_col.dbcol` | `default_sleeve_col.dslcol` |
| 男性变体 `_man` | ✅ 有（`GetManFileName`） | ❌ 无 |
| CRC 变体（名字加 `2`） | ✅ 有（`GetCRCFileName`，仅 KCES2 头发走默认文件时） | ❌ 无 |
| 建碰撞器的方式 | 先建 `NativeXxxCollider`（给一代摇曳骨用），**再转换**成 Magica 碰撞器 | **直接建** Magica Cloth 2 碰撞器 |
| 作用于几个 `MagicaCloth` | 1 个 | 2 个（左右袖共用同一个文件） |
| ExportCM 旁车格式 | 纯 JSON | `BinaryWriter.Write(string)` 前缀 + JSON |

### 6.2 ⭐ `.dslcol` 支持的碰撞器类型和 `.dbcol` 不一样

因为 `.dslcol` 是**直接**建 Magica Cloth 2 碰撞器（`DynamicSleeveBone.CreateMagicaCloth2Collider`），不经过 Native 中转，支持的类型反而和 `.dbcol` 不同：

| 类型 | `.dbcol`（头发 / 挂件） | `.dslcol`（袖子） |
|---|---|---|
| `Capsule` | ✅ `MagicaCapsuleCollider` | ✅ `MagicaCapsuleCollider` |
| `Sphere` | ✅ `MagicaSphereCollider` | ✅ `MagicaSphereCollider` |
| `Plane` | ❌ 丢弃 | ✅ **`MagicaPlaneCollider`** |
| `MaidPropCol`（随体型变化） | ✅ `MagicaCapsuleCollider` | ❌ **不在 switch 里，静默忽略** |
| `limbEnableList` 开关肢体碰撞器 | ❌ 无效（恒全开） | ✅ **有效**（`limbMagica2ColliderDictionary[t].enabled = isEnable`） |

两条结论：

- **只有袖子能用平面碰撞器。** 第 6 课那个"平面碰撞器圈住范围"的技巧在 KCES 里唯一还能用的地方就是袖子。
- **只有袖子能关掉肢体碰撞器。** 头发挂件想让某条肢体不参与碰撞，只能靠调 `radius` / `motionConstraint` 绕，或者接受它。

### 6.3 顺带一提：`.dslconf` 是死代码

`DynamicBoneMgr.LoadDynamicSleeveBoneSetting`（读 `.dslconf` 和 Magica Cloth **一代** 的袖子碰撞器）在 KCES 1.34.5 和 KCES2 1.36.0 里**都没有任何调用方**。两代的袖子分支都只走 `InitializeMagica2Cloth` + `LoadMagica2DynamicSleeveBoneSetting`。

所以袖子实际上**只有 Magica Cloth 2 这一套实现**，`.dslconf` 不用做。

<br>

## 七、名字像碰撞器但和 MOD 无关的文件

看到别搞混。这几个文件名字里都带 col / collider / hit，但和摇曳骨都没关系，而且**都不是 MOD 能提供的**：

| 文件                                                                       | 作用                                                             | 为什么你找不到它                                                                                                                                                   |
|----------------------------------------------------------------------------|------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `limbconf.limbcol`                                                         | 全局的 8 个肢体胶囊碰撞器定义                                    | 在 `system*` 类的包里，全局唯一，不随部件走                                                                                                                        |
| `maidIKCollider.ikcol` / `manIKCollider.ikcol` / `ik_collider.ikcol.bytes` | 全身 IK 的碰撞器                                                 | 同上，与摇曳骨无关                                                                                                                                                 |
| `maid_collider.bytes` 一族                                                 | **VR 抓取 / 触摸判定**用的身体胶囊，以及编辑模式摆放部件时的碰撞 | COM3D2 / COM3D2.5 里用 `Resources.Load("System/maid_collider")` 读，**打包在 `resources.assets` 里而不在任何 `.arc`**，只能用 AssetStudio 之类的工具从游戏本体里掏 |
| `<区域>.hitcheck`                                                          | CM3D2 时代的**身体碰撞球**集合                                   | 只有 4 个全局文件，而且 **KCES 完全不读它**（见 7.2）                                                                                                              |
| `xxx.sad`                                                                  | 部件挂载点的位置 / 旋转 / 缩放                                   | 只在 KCES 导出到 COM3D2 时生成，KCES 自己不读（见 7.3）                                                                                                            |

### 7.1 `maid_collider.bytes` 一族

内容是"count + N × (骨骼路径, center, direction, height, radius)"的裸二进制胶囊列表，挂到身体骨骼上，用 layer 17 / 19 参与射线和触碰判定。

| 文件 | 用途 | 生成的对象名前缀 |
|---|---|---|
| `maid_collider.bytes` | 抓取（Grab） | `OvrGrabHit_` |
| `maid_collider_touch.bytes` | 触摸（Touch） | `OvrTouchHit_` |
| `maid_collider_crc.bytes` | CRC 身体的抓取（**仅 COM3D2.5**） | `OvrCrcGrabHit_` |
| `maid_collider_v2.bytes` | CRC 身体的触摸（**仅 COM3D2.5**） | `OvrCrcTouchHit_` |

`Ovr` = Oculus VR。COM3D2 / COM3D2.5 里被 VR 触摸场景、TouchAction、编辑模式的部件摆放窗口调用。

**KCES2 里这套代码是休眠的** —— `MaidColliderCollect` 的所有公开方法（`AddCollider` / `AddColliderCollect` / …）在 KCES2 的程序集里**没有任何调用方**，而且它只声明了前两个文件。KCES 没有 VR 模式，用不上。

顺带一提 `MaidColliderCollect.Write()` 是 KISS 内部的编辑器函数，写到 `Application.dataPath/Resources/` 下 —— 在发行版里跑不通。

### 7.2 `.hitcheck`

**不是每个部件一个，全游戏只有 4 个**，文件名来自 `TBody.m_strDefSlotName` 三元组的第三项（身体区域）：

`IK.hitcheck`、`Jyouhanshin.hitcheck`（上半身）、`Kahanshin.hitcheck`（下半身）、`Uwagi.hitcheck`（上衣）

格式是裸二进制：`"HitCheck"` 字符串 + 球体数量 + N × (type, len, len², 骨骼名, 父骨骼名, localPosition, SKRT, RL)。

用途是 CM3D2 时代 `TBoneHair_` 那套物理的身体碰撞球：

- 老头发 / 老裙子骨骼撞身体（`SphereMove_hair`）
- 手部触摸判定（`m_listHandHitL/R`、Leap Motion 手）
- 体型滑条会缩放这些球（`ScaleMune("MUNE" / "HARA" / "MOMO" / "KOSHI_SCL" / "KOSHI_SVAL", …)`），`SKRT == 99` 标记的是胸部的球，`SKRT` 1/2/3 是裙子的分层

**KCES1 和 KCES2 里 `TBodySkin.LoadHitcheckData` 是个空方法** —— 整套 hitcheck 不加载、不使用。KCES 的身体碰撞改由 `limbconf.limbcol` 的 8 个肢体胶囊 + 各部件自己的 `.dbcol` / `.dslcol` 承担。

COM3D2.5 里它还在用，但 CRC 身体走的那条分支会把扩展名拼两次（`fn + ".hitcheck" + ".hitcheck"`），看起来是个 bug，实际效果是 CRC 身体也读不到 hitcheck。**这条未实机确认**，只是读反编译代码得到的判断。

生成端是 `TBodyHit.SetSphere`，写到 `G_Assets/_DAT/hitcheck` —— KISS 内部的开发资产路径。

### 7.3 `.sad`（SAVED_ATTACH_DATA）

保存的是**玩家在 KCES 里手动调整过的"部件挂载关系"**：某个挂件被挂到了哪个部件的哪个自定义挂载点 / 顶点上，以及它的位移、旋转、缩放。

它的生命周期只有一个方向：

```
KCES2 编辑器（数据存在 preset 的 savedAttachPos 里）
      ↓ 导出角色到 COM3D2
<导出名>_<slotid>[<subno>].sad     ← 只在这一步产生
      ↓ 生成的 .menu 里写一条 crc_part_hide_move <slot> <sad文件名>
COM3D2.5 装备时 ImportCM.ImportSavedAttachDataGP03 读它
      ↓
TBodySkin.LoadOrNewAttachPoint 复现挂载点和变换
```

格式是 `"SAVED_ATTACH_DATA"` + 版本 2000 + 记录数 + N 条 `SavedAttachData`（记录版本 2000 或 2001，2001 多一个 `TargetSlotNo`）。

**KCES 自己从来不读 `.sad`**，它把这些信息存在角色预设里。所以你在 KCES 的资源包里找不到 `.sad`，只有导出文件夹里才有。


<br>

## 八、文件名与查找顺序

`parts_name` = 模型根 GameObject 名去掉 `_SM_`，也就是 Blender 里那个黄色倒三角的名字。

### KCES / KCES2 内部

| 类型           | 第 1 顺位                 | 兜底 1                                          | 兜底 2                   | 兜底 3            |
|----------------|---------------------------|-------------------------------------------------|--------------------------|-------------------|
| 头发 / 挂件    | `<parts_name>.db2conf`    | `default_<mpn>.db2conf`（头发另试 `_man` 后缀） | `default_yure.db2conf`   | 代码内硬编码 JSON |
| 裙子           | `<parts_name>.dsb2conf`   | `default_skirt.dsb2conf`                        | —                        | 代码内硬编码 JSON |
| 袖子           | `<parts_name>.dsl2conf`   | `default_sleeve.dsl2conf`                       | —                        | —                 |
| 一代摇曳骨     | `<parts_name>.dbconf`     | `default_<mpn>.dbconf`                          | `default_yure.dbconf`    | —                 |
| 头发挂件碰撞器 | `<parts_name>_col.dbcol`  | `default_<mpn>_col.dbcol`                       | `default_yure_col.dbcol` | —                 |
| 袖子碰撞器     | `<parts_name>_col.dslcol` | `default_sleeve_col.dslcol`                     | —                        | —                 |

`<mpn>` 是小写的 MPN 名，比如 `default_hairf.db2conf`、`default_acckamisub.dbconf`。老 COM3D2 头发还会优先尝试 `old_` 前缀版本。

> **`.dslcol` 的 `_col` 中缀是个坑**：KCES 内部找的是 `<parts_name>_col.dslcol`（带 `_col`），但 ExportCM 导出时写的是 `<model名>.dslcol`（**不带** `_col`），COM3D2.5 也是按 `<model名>.dslcol` 找的。所以同一份袖子碰撞器给两边用时文件名不一样。`.dbcol` 两边都带 `_col`，没这个问题。

### ⚠ COM3D2.5 内的命名规则不一样

COM3D2.5 优先用的是 **`.model` 文件名（去扩展名）**，而不是 GameObject 名：

| 类型                                  | 第 1 顺位               | 兜底                                                                         |
|---------------------------------------|-------------------------|------------------------------------------------------------------------------|
| `.db2conf`                            | `<model文件名>.db2conf` | `default_<mpn>.db2conf` → `default_yure.db2conf`（**没有 parts_name 兜底**） |
| `.dsb2conf` / `.dsl2conf` / `.dslcol` | `<model文件名>.xxx`     | `<parts_name>.xxx` → `default_xxx`                                           |
| `.dbconf` / `_col.dbcol`              | `<model文件名>.xxx`     | `<parts_name>.xxx` → `default_xxx`                                           |

所以做给 COM3D2.5 用的头发 / 挂件，`.db2conf` 必须跟 **`.model` 文件名**同名，跟 Blender 里的对象名同名是不够的。

### 8.1 文件名上的四种修饰

同一个物理文件在不同条件下会被换名再找一遍。四种修饰规则如下，**它们只作用在默认文件（`default_*`）上，你自己的 `<parts_name>.xxx` 不会被改名**（`_man` 那条对 `.dbconf` / `.dbcol` 是例外，见下）：

| 修饰            | 变形方式            | 触发条件                                                                                | 举例                           |
|-----------------|---------------------|-----------------------------------------------------------------------------------------|--------------------------------|
| `old_` 前缀     | `old_<原名>`        | 部件是从 COM3D2 转来的老头发（`ver` 在 100–199）                                        | `old_default_hairf.db2conf`    |
| `_man` 后缀     | `<名>_man<扩展名>`  | `.dbconf` / `.dbcol`：角色是男性（`boMAN`）<br>`.db2conf`：`.menu` 文件名里含 `_mhair_` | `default_hairf_man.db2conf`    |
| `2` 后缀        | `<名>2<扩展名>`     | `.dbcol` 走默认文件、且这是 KCES2 型头发                                                | `default_hairf_col2.dbcol`     |
| `default_<mpn>` | 用 MPN 名代替部件名 | 找不到 `<parts_name>` 的文件                                                            | `default_acckamisub_col.dbcol` |

`<mpn>` 是**小写的 MPN 枚举名**：`hairf` / `hairr` / `hairs` / `hairt` / `hairaho` / `acckamisub` / `acckubi` …… 和 COM3D2 的 MPN 同名。

改名后的文件如果也不存在，就继续往下一级兜底，所以这些修饰不会导致加载失败，只会静默地用上一级的参数。

### 8.2 固定名文件（MOD 改不了）

这些不跟部件走，全游戏只有一份，名字是写死在代码里的：

| 文件名                      | 内容                                         | 从哪读                                                                                  |
|-----------------------------|----------------------------------------------|-----------------------------------------------------------------------------------------|
| `limbconf.limbcol`          | 8 个肢体胶囊碰撞器                           | `CatalogType.Unknown`（全类目搜）                                                       |
| `maidIKCollider.ikcol`      | 女性全身 IK 碰撞器                           | `CatalogType.System`                                                                    |
| `manIKCollider.ikcol`       | 男性全身 IK 碰撞器                           | 同上                                                                                    |
| `ik_collider.ikcol.bytes`   | IK 碰撞器（`IKColliderSaveLoader` 的默认名） | 同上                                                                                    |
| `maid_collider.bytes`       | VR 抓取判定胶囊                              | KCES：`CatalogType.System`；COM3D2 / COM3D2.5：`Resources.Load("System/maid_collider")` |
| `maid_collider_touch.bytes` | VR 触摸判定胶囊                              | 同上                                                                                    |
| `maid_collider_crc.bytes`   | CRC 身体的抓取（**仅 COM3D2.5**）            | `Resources.Load("System/maid_collider_crc")`                                            |
| `maid_collider_v2.bytes`    | CRC 身体的触摸（**仅 COM3D2.5**）            | `Resources.Load("System/maid_collider_v2")`                                             |
| `IK.hitcheck`               | IK 区域的身体碰撞球                          | 部件类目（**KCES 不读**）                                                               |
| `Jyouhanshin.hitcheck`      | 上半身                                       | 同上                                                                                    |
| `Kahanshin.hitcheck`        | 下半身                                       | 同上                                                                                    |
| `Uwagi.hitcheck`            | 上衣                                         | 同上                                                                                    |

`.hitcheck` 那四个名字来自 `TBody.m_strDefSlotName` 三元组的第三项（身体区域标签），不是 MOD 作者能加的第五个区域。

`maid_collider*.bytes` 在 Unity 里是**名为 `maid_collider` 的 TextAsset**（`.bytes` 只是 Unity 导入二进制的约定后缀），所以按 `maid_collider` 这个名字查找，文件系统里看不到 `.bytes`。

### 8.3 导出产物的命名（KCES → COM3D2）

用 KCES2 的"导出角色到 COM3D2"时，物理文件的名字**换成按 `.model` 文件名走**（`ExportCM.ExportPhysicsDef` 里 `text = Path.GetFileNameWithoutExtension(modelFileName)`）：

| 文件                    | 导出名                                    |
|-------------------------|-------------------------------------------|
| 一代摇曳骨参数          | `<model名>.dbconf`                        |
| 头发挂件碰撞器          | `<model名>_col.dbcol`                     |
| Magica Cloth 2 头发挂件 | `<model名>.db2conf`                       |
| Magica Cloth 2 裙子     | `<model名>.dsb2conf`                      |
| Magica Cloth 2 袖子     | `<model名>.dsl2conf`                      |
| 袖子碰撞器              | `<model名>.dslcol`（**注意没有 `_col`**） |
| 挂载点数据              | `<导出名前缀>_<slotid 小写>[<subno>].sad` |

`.sad` 那个方括号是文件名的一部分，比如 `gp03_export_<GUID>_acckamisubl[0].sad`。它会被写进生成的 `.menu`：

```
crc_part_hide_move	<slotname>	<sad 文件名>
```

KCES 自己不读 `.sad`，只有 COM3D2.5 会读。


<br>

## 九、工具

| 工具 | 用途 |
|---|---|
| [MeidoSerialization](https://github.com/MeidoPromotionAssociation/MeidoSerialization) | Go 序列化库 + CLI + gRPC + MCP，原生格式 ↔ 编辑 JSON 互转，支持上面所有扩展名 |
| [KCES MOD EDITOR](https://github.com/MeidoPromotionAssociation/KCES_MOD_EDITOR) | 图形化编辑器（开发中），每种格式一个页面，支持表单和 Monaco JSON 双模式 |
| [COM3D2 MOD EDITOR](https://github.com/MeidoPromotionAssociation/COM3D2_MOD_EDITOR) | COM3D2 侧的姊妹项目，负责 `.phy` / `.col` / `.psk` |

游戏内实时调参目前**没有现成工具**：KCES2 和 COM3D2.5 都没有内置的物理参数编辑界面，`SaveMagica2Setting` 的唯一调用方是导出功能。好消息是 `MagicaCloth.SerializeData` 是公开字段，改完调一次 `SetParameterChange()` 就生效，写个 BepInEx 插件比当年戳 Dynamic Bone 简单得多。
