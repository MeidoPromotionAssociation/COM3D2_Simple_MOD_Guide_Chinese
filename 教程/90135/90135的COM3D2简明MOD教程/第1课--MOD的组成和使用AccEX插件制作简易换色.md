# 第 1 课，MOD 的组成和使用 AccEX 插件制作简易换色

有些图是以前的版本截的，现在的版本会有一点点不一样，但不重要，一样的格式。

## MOD 的组成

在 3D 游戏开发中，游戏内的物体本质上都是由 3D 模型组成。

一个完整的 3D 模型通常包含以下几种元素：

- **Mesh（网格）**：定义物体的形状，包括顶点、边和面等信息。
- **Shader（着色器）**：决定物体的渲染方式，如光影效果、透明度等。
- **Material（材质）**：定义贴图如何被渲染到网格上，包含与着色器的关联。
- **Texture（纹理）**：一种图片资源，用来描述物体表面的细节，如颜色、光滑度等。
- **Texture Map（贴图）**：进一步细分的纹理，用于不同用途，例如法线贴图、光照贴图、UV 贴图等。
- **Model（模型）**：包含以上几种信息的文件（也可能只包含部分）。

中文环境中平时基本把 Texture 和 Texture Map 都叫做贴图，其实叫做纹理和映射会更准确。

简单来说：
1. **模型（Model）**：模型是一个可以包含多种信息的文件，其中可以包含物体的点线面信息（称为网格）定义物体形状，包含材质信息等。在 COM3D2 中是 `.model` 文件，且 `.model` 文件可以包含材质和着色器等信息，因此部分情况下可以省略 `.mate` 文件。
2. **贴图（Texture）**：定义物体的表面细节，例如颜色和质感。COM3D2 中对应 `.tex` 文件（就是图片，但是 3D 图形硬件要求纹理以专门格式进行压缩，所以格式不同）。
3. **材质（Material）**：决定模型渲染出来应该是什么样的。COM3D2 中使用 `.mate` 文件（`Material` 的缩写）。
4. **着色器（Shader）**：决定物体的渲染方式，例如是否有轮廓线、透明效果等。在 `.mate` 文件中指定。

### COM3D2 特有的文件类型
以下是一些特定于 COM3D2 的文件：
- **`.menu` 文件**：游戏物品栏中看到的每个物品几乎都由 `.menu` 文件定义，用于指定这个物品使用什么模型、使用什么图标等等，包括男选择界面，以及脱下物品的选项都是一个个 `.menu`。
- **`.pmat` 文件**：用于定义渲染顺序，一般只在使用透明着色器时有用。
- **`.phy` 文件**：物理信息文件，提供物体的物理行为，例如摇动效果。
- **`.col` 文件**：碰撞箱信息文件，用于实现物体之间的物理交互。
- **`.psk` 文件**：专用于裙子物理模拟的信息文件。
- **`.anm` 文件**：动画文件，可以为模型创建动画，游戏内的动作基本都是使用 .anm 保存和读取的。

### 最简化的 MOD
制作一个最精简的 MOD，通常只需要以下文件：
- **`.menu`**
- **`.tex`**：
- **`.model`**：

甚至 .tex 都可以不需要（没有的情况下需要 ExtendedErrorHandling 错误处理插件，不然游戏可能会退出）

甚至有的插件可以直接呼出 .model 而不需要 .menu

<br>

#### 最简单来说

- **网格**：物体本身的点线面等 3D 信息
- **贴图**：一张图片
- **UV 贴图**：贴图怎么贴到网格上
- **着色器**：渲染程序，比如会发光、半透明等
- **材质**：贴图+着色器+... 等各种信息的复合体
- **模型**：网格+UV 贴图+材质+... 等各种信息的复合体

<br>

- **.model**：模型（不含贴图，含 UV 贴图，材质可以被 .mate 覆盖）
- **.mate**：材质（不含贴图）
- **.tex**：贴图

<br>

大概理解组成部分以后

我们通过案例来加深对 MOD 组成的理解

## 提示
在制作 MOD 的过程中，经常会遇到需要修改一点内容并立即在游戏中查看效果的需求。大部分情况下并不需要每次都重启游戏。

看不懂可以先跳过这个部分。

**文件准备**
1. 在游戏启动时，游戏会扫描 Mod 文件夹并把可以识别的文件都记忆到一个虚拟的 arc 文件系统中。游戏识别有没有这个文件是通过 arc 进行的，因此在游戏启动后新增文件不会被识别。
2. arc 只会记住文件的名称，而不会记住其内容，也不会记住路径（如果你使用 MaidLoader 的话是这样的）
3. 由于 arc 不区分目录，因此同名文件只会记忆最后被读取的那个
4. arc 记住文件名以后并不会去读取文件内容。（当然进入加载模式时游戏会读取 .menu 内的图标和物品名字/描述都，否则你就看不到物品列表了）
5. 在你装备物品时，游戏才会去实际文件夹中实时读取 .menu 文件，如果 menu 中引用了其他文件，那么看看 arc 内有没有记住这个文件，如果有就实时从文件夹内读取（称为热加载）
6. 因此我们可以通过在启动游戏前就准备好相关文件，来使用热加载机制。

**热加载方法**
1. 在游戏运行过程中，你可以直接修改以下几种文件，而不需要重启游戏：
   - `.menu` 文件（部分内容无法热加载）
   - `.mate` 文件
   - `.model` 文件
   - `.tex`文件（如果你装了缓存插件（mate_tex_chache.cs 之类的），那就不能热加载，此时可以通过 AccEX 插件修改查看）


2. 修改完成后：
   - 切换到游戏中的其他物品。
   - 再切换回来你的目标物品。
   - 游戏会读取修改后的最新内容。
  

**注意事项**
1. 部分内容无法通过热加载刷新：
   - .menu 文件中的物品图标数据无法热加载。（可以通过重新进入编辑模式来刷新，但个人不推荐这个办法）
   - .menu 文件中的物品名称和描述无法热加载。
   - 新增的文件无法通过这种方法直接加载，可能导致游戏出现 BUG。

2. 如果需要加载新增文件：
   - 使用 MaidLoader 的刷新功能，可以将新增文件加载进游戏。
   - 但不推荐使用，因为有时候加载了并不能生效，或是会出现其他问题。

通过以上方法，你可以高效地修改和测试 MOD，而无需频繁重启游戏。

MaidLoader 有一个 QuickMod 功能，可以在文件被修改时重新创建 arc 文件系统，有需要的可以看看。


## MOD 是如何加载的

其实游戏官方是有 MOD 支持的。

从 CM3D2 v1.0.4 开始，在编辑界面同时按下 M O D 三个键，就会出现一个导出选项，这个就是官方提供的功能。

这个官方支持就是 `.mod`  格式的文件，只要把它放进 MOD 文件夹就可以生效。

.mod格式的 MOD 称之为公式 MOD/官方 MOD。

其他格式/方式实现的 MOD 称之为非公式 MOD/非官方 MOD。

<br>

不过这个官方支持并不算非常全面，最开始官方只打算让你修改贴图，没有修改模型等功能。许多高级需求（如覆盖已有文件、动态刷新等）必须通过其他办法才能实现。

因此，玩家们在思考如何制作自己的模型等等，而不是仅仅修改贴图。

于是乎出现了 Sybaris 等工具，可以直接加载官方格式的的文件，而不是有限制的 .mod。

<br>

为了修改模型，2015 年 8 月 6 日，saidenka 大神开始着手开发 Blender-CM3D2-Converter 插件。[来源](https://github.com/CM3D2user/Blender-CM3D2-Converter/commits/master/?after=add1bc104633837cb7a0574ca30b73992de1dc6f+979)

而后在 2015 年 10 月前发布的 [CM3D2 v1.11](https://www.kisskiss.tv/cm3d2/update.php) 版本中，官方添加了可以从 MOD 文件夹中直接读取 `*.menu,*.model,*.mate,*.anm,*.tex` 文件的能力

![图片](https://github.com/user-attachments/assets/326ab08f-1f6c-419f-83b5-580182a77553)

从此非公式 MOD 的读取有了官方支持。

然而在 2015 年 10 月 30 日，官方才提供了在 Metasequoia 建模软件中修改模型的方法[来源](https://www.kisskiss.tv/kiss/diary.php?no=598)

总之就是公式 MOD 限制太多，在社区发明出替代方案以后，就没有多少人使用公式 MOD 了，官方也知道这一点，添加了支持直接读取原格式的功能。

于是呢，大家都去制作非公式 MOD 了。

最终，KISS 对公式 MOD 的制作支持与 2023 年 1 月 20 日结束。

<br>

来到 COM3D2 时代，更是没有人使用 .mod 了。

这次出现了 [Modloader](https://github.com/Neerhom/COM3D2.ModLoader) 插件

它直接拦截官方读取文件的函数，让游戏在访问文件系统时先检查 ModLoader 的目录与缓存，如果没有再从官方路径中查找，从而允许我们直接加载几乎任何官方物品格式的文件。(.menu .mate 之类的)(还可以覆盖官方的文件)

因此官方物品有什么功能，我们就可以使用什么功能。

因此做 MOD 的底层逻辑，其实就是对官方物品进行逆向，学习官方是怎么做的，从而做出自己的物体。

注：Modloader 也已经淘汰了，请使用 [MaidLoader](https://github.com/Pain-Brioche/COM3D2.MaidLoader) 它们的基础功能是一样的，但是 MaidLoader 更强大。

更详细的信息请参考
 - [https://github.com/Neerhom/COM3D2.ModLoader](https://github.com/Neerhom/COM3D2.ModLoader)
 - [https://github.com/Pain-Brioche/COM3D2.MaidLoader](https://github.com/Pain-Brioche/COM3D2.MaidLoader)

<br>

但是我们仍然需要 Modloader 来做一些覆盖官方文件的操作等等。

其实官方也做了用 MOD 覆盖官方文件的功能，但是还是日本法律问题，官方选择禁用

![图片](https://github.com/user-attachments/assets/1c4c0c4b-68cd-4062-8186-c76d3d3e0692)

```
以下のフラグをtrueにするとMODフォルダのファイルが優先されるが、モザイクも安易に外すことが可能になる為に現在はオミット。

如果将以下标志设置为 true，则 MOD 文件夹中的文件将优先加载，但由于可以轻松删除马赛克，因此目前忽略此功能。
```

包括代码可以看到以前游戏还有游戏内编辑换色 MOD 的功能，部分文件格式也有序列化写出的功能等等

所以，并不是 KISS 不想支持，而是不能。

<details>

<summary>Unity 修改届背后的男人</summary>

如果你在任意工具链往下挖掘，你会发现几乎所有基础代码都是由 https://github.com/ghorsington 创建的（https://github.com/denikson）

包括 BepinEX、UmaiUme、COM3D2.i18nEx 、CM3D2.MaidFiddler、CM3D2.YATranslator、IMGUITranslationLoader 等

都是马男为 COM3D2 创建的……

我们的马男为了玩 COM3D2 创建了整个工具链，太强大了马男

而如今 BepinEX 等工具广泛用于其他游戏，包括隔壁的 ILLUSION。

</details>





## 对官方物品进行修改

### 提取官方物品

以官方的 dress118_stkg_i_.menu 为例

#### 找到文件名

方法 1 ：按 Shift + I 打开，或使用齿轮菜单栏按钮 PropMyItem InoryS 版

在袜子栏搜索 118，我有这么多是因为我有 MOD

![图片](https://github.com/user-attachments/assets/abc4759c-9b2f-4d25-9235-18bd5d07e79a)

装备上后在控制台可以看见这个物品的 menu 名称（只有 InoryS 版才会显示）

方法 2：在编辑模式一起按下 M O D 三个键，左上角就会有一个导出，此时装备一个物品，然后点导出，就能看到文件名。（这是制作公式 MOD 用的功能，不过我们只是用来看一下文件名）

方法 3：用 AccEX 插件（F12）点导出时也会显示文件名.

#### 提取文件

官方物品都打包在 .arc 文件内，使用 SybarisArcEditor 可以打开 .arc 文件，因我们使用该工具从游戏的 .arc 文件内提取这个袜子的相关文件。

如果你没有这个，去首页的英文工具包里找到，然后把 SybarisArcEditor.exe 放到 COM3D2.exe 旁边就行了，该程序会自行查找游戏的所有 .arc 文件。

![图片](https://github.com/user-attachments/assets/68c9f72a-2dcc-4dc6-80ad-0bcdbd06deec)

打开后会很卡，耐心等一会

这个程序没做防抖，你输一个字他就搜索一次就会很卡，所以建议直接粘贴进去

然后你会发现 dress118_stkg 什么都搜不到

那是因为这是从 CM3D2 联动过来的，这就是为什么有的东西你找不到

去 CM3D2 那边打开 SybarisArcEditor（同理把 SybarisArcEditor.exe 放到 CM3D2.exe 旁边就行了）

可以看到官方文件种类和 dlc 名称进行了分类

![图片](https://github.com/user-attachments/assets/9cc53d34-156c-4126-8d79-4582741b7d85)

依次点开，你会发现有 .mate .menu .model 和几个 .tex

依次右键，点保存

得到以下几个文件，还记得之前说的组成吗？

![图片](https://github.com/user-attachments/assets/6ae375de-212f-4868-9ae8-7127e42d9939)

### 修改官方物品

很久以前， KISS 官方推出了 .mod 格式，现在已经淘汰了

随着官方的支持和 Modloader、MaidLoader 的出现，我们可以直接以官方物品的格式来加载物品，因此官方有什么功能，我们就可以使用什么功能。


#### menu

用编辑器打开 .menu 文件

这里用我自己做的 menu 编辑器打开，本来我都是用某 D 的工具，但是他禁止外国人使用，不好分发。

为了大家有相对好用的工具，只好自己做了

请到这里下载：[https://github.com/90135/COM3D2_MOD_EDITOR](https://github.com/90135/COM3D2_MOD_EDITOR)

下面的图片是 V1 版本的，现在的界面已经不长这样了，但格式是一样的，理解就好。

![图片](https://github.com/user-attachments/assets/fc89861f-debb-47e4-bb63-54e443bf35e6)

可以看到 menu 的基本组成
<br>

menu 文件详细请参附录，或 https://seesaawiki.jp/com3d2mod_wiki/d/menu

<br>

我们把东西都改个名，比如都改成 learn

注意文件名不能以 mod 开头，不然你会发现它读不了

(MODフォルダにMODファイルが見つかりません。)

<details>

<summary>因为和官方 .mod 格式冲突</summary>

![图片](https://github.com/user-attachments/assets/ca35929d-6630-4777-a730-a67512fc630a)

![图片](https://github.com/user-attachments/assets/e85738d9-84f6-4f17-bddc-b0989d26d02c)

</details>



![图片](https://github.com/user-attachments/assets/b278da2a-8430-4f6b-a1d1-64f55c8b9528)


<br>

然后打开 menu 编辑，编辑指向对应文件

![图片](https://github.com/user-attachments/assets/b3895ac4-c11a-4f96-8a8a-c95e5553a4d0)

#### mate

那么你会问其他的 2 个 .tex 是在哪里指定的？

答案是，可以在 .model 或 .mate 里指定

.mate 可有可无，如果你指定了 .mate，那么就会覆盖 .model 里面指定的内容（所以可以通过添加多个 .meun 和 .mate 组合来制作换色 mod 而不需要修改 .model）

这个官方物品使用了 .mate 所以我们这里主要关注 .mate 文件，.model 内的信息在第三课

<br>

用 mate 编辑器或者转成 txt 打开 .mate 文件

![图片](https://github.com/user-attachments/assets/ca1510ea-bd92-4eb7-97df-4412c215dfe5)

改成对应贴图名字，但不带 .tex

那个路径没什么用，可以忽略，随便填个东西就行了(COM3D2 2.06 +)

![图片](https://github.com/user-attachments/assets/f849350f-f2fe-4de6-9974-64b36b1b743f)

<br>

mate文件详情请参考

https://seesaawiki.jp/com3d2mod_wiki/d/mate

#### tex

用 TexTool 打开 .tex，会转成 .png

用 TexTool 打开其他格式图片，会转成 .tex

打开 mod_learn.tex 用你最爱的图片编辑器编辑一下

作为示例，我们这里乱画一点东西

![图片](https://github.com/user-attachments/assets/06ac51a8-9b8b-4133-a90d-0c7f5b569a30)

然后再转回 .tex

同理 mod_learn_i_.tex 这个是我们在 menu 里面指定的图标，也是画点东西在上面

![图片](https://github.com/user-attachments/assets/5998590b-a56f-4965-8df2-310817490638)

#### 进游戏

把刚刚的文件都放到 MOD 文件夹里，然后启动游戏

![图片](https://github.com/user-attachments/assets/025de591-96bb-4fdd-b954-d97298d879dd)

铛铛
发现刚刚乱画的线条了吧

![图片](https://github.com/user-attachments/assets/842f4a42-5f16-4537-b622-89b980278cf1)

然后我们发现后面的贴图并没有被画

取决于光照方向，非光照面会使用 _ShadowTex 中的贴图，我们没有修改，所以不会有变化

![图片](https://github.com/user-attachments/assets/634f4663-6e93-4c04-93a2-fa69e28f7850)

<br>

恭喜你已经学会了 MOD 的基本组成

### 对别人的 MOD 进行修改

同理，只不过不需要提取了罢了

使用 ShiftClickExplorer 插件，编辑模式“shift+左键”物品即可转跳到 MOD 文件所在位置。

插件地址都在首页

## 使用 AlwaysColorChangeEx

我们可以使用 AlwaysColorChangeEx 轻松完成刚刚的操作和编辑 mate，插件地址都在首页

默认快捷键 F12

### 和 mate 文件的对应关系

![图片](https://github.com/user-attachments/assets/ebb230ac-49a0-49cc-b886-ff9b4fdee61d)

color 只会对 _MainTex 生效

![图片](https://github.com/user-attachments/assets/270ecffc-2901-4a5c-86b3-c1bdcb844dde)

![图片](https://github.com/user-attachments/assets/7f6202c8-4979-441b-8b0a-829f1d2b6bea)

点纹理更改按钮

![图片](https://github.com/user-attachments/assets/864b107f-8cc4-48ff-8347-477770026c97)

点这个就能更换贴图了
可以直接读 .png 格式的

![图片](https://github.com/user-attachments/assets/ea22d8b3-ecf0-48d6-9081-68b2a3015dee)

你可以自己调节各种参数尝试,，然后点击 menu 导出就能导出了
会导出到 MOD/ACC 文件夹

#### 素材包

本人提供的 MOD Thicc_Socks_109 中 提供了许多贴图，你可以自己更换尝试，非常有助于理解

- https://zodgame.xyz/forum.php?mod=viewthread&tid=470514&extra=page%3D1
- https://mega.nz/folder/U6Jy0a6a#Pv5G9G_J5zoYc46TVmz6iA

### 使用 AlwaysColorChangeEx 来修改 MOD

#### 修改着色器

如图就是着色器，你可以自己试试

比如我们这里选择透明着色器，但是发现腿没了

![图片](https://github.com/user-attachments/assets/09cad8a5-af87-491e-9ba3-9734b721ca32)

还记得 menu 文件里的   消去node設定開始  吗

![图片](https://github.com/user-attachments/assets/816ed6a9-5e23-434d-b44f-83b87594b0a8)

就是它把腿隐藏了
我们把它删了再看看

![图片](https://github.com/user-attachments/assets/d6bd7f5f-7ea8-4a34-b2a0-49fa1e584b42)

可以看到大腿就显示出来了，但是有点穿模

![图片](https://github.com/user-attachments/assets/682da734-3d70-4d99-9373-8849d2405862)

**注意：AlwaysColorChangeEx 导出的 MOD 位置在 MOD 文件夹的 ACC 文件夹里面，不在原位置**

#### 渲染顺序

RQ 就是 RenderQueue 渲染顺序

一般透明材质要设置为 3000+

可以看出明显区别

![图片](https://github.com/user-attachments/assets/4a77035e-a774-4ad5-9c72-ca01c4e216bb)

![图片](https://github.com/user-attachments/assets/eba40c63-f0fa-4f79-9292-b11da2fe47e0)

鞋子我也调成透明材质了
它的 RQ 是 3071
可以看到，当我们把袜子渲染顺序调到 3072 后，鞋子的显示发生了变化

![图片](https://github.com/user-attachments/assets/416ff4bb-8e09-453d-859c-341c34a76470)

要保存 RQ，需要使用 .pmat 文件。

使用 .pmat 的前提是需要 .mate, 在 .mate 文件的这一栏可以指定 .pmat 文件名

![图片](https://github.com/user-attachments/assets/52e45c33-ff54-45e7-aaeb-b55c27d6858e)

使用编辑器直接另存为即可新建一个 .pmat，然后在 .mate 中指定，然后 AccEX 插件导出时就能导出 .pmat 了，否则 AccEX 是不会导出 .pmat 的。

当然你也可以手写，编辑器内对所有参数都有说明

![图片](https://github.com/user-attachments/assets/0748f18c-21b8-4d2d-9dda-acd3ebc25c7c)


## 作业

* 将 `dress118_stkg` 恢复原贴图。
* 将贴图颜色改为蓝色。
* 设置为透明材质并调整 `RQ` 顺序。
* 导出为完整的 MOD 文件。

大致效果

![图片](https://github.com/user-attachments/assets/f621329d-5c1f-45bd-a14f-704cc13023e3)

