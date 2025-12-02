# COM3D2 中文 MOD 教程

[![CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese)
[![GitBook](https://img.shields.io/static/v1?message=GitBook&logo=gitbook&logoColor=ffffff&label=%20&labelColor=5c5c5c&color=3F89A1)](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese)

COM3D2 Chinese MOD Tutorial

For other language users, just use [https://immersivetranslate.com/en/](https://immersivetranslate.com/en/)

That is a browser plugin that allows you to use Google or AI translation, etc.

These documents are open source, and you are welcome to submit translations

<br>

由于有越来越多的作者贡献教程，本仓库转为更通用的 MOD 教程仓库。

包含多位作者的教程，仓库也从个人名下转移至组织。

为了使旧连接尽量不失效，仓库命名以及 gitbook 地址未更改，敬请谅解。

<br>

这些文档使用 CC BY-NC-SA 4.0 许可证进行共享，欢迎各位作者进行贡献！

您可以通过群联系，或直接发送 Pull Request

敬请注意仓库许可证

<br>

欢迎提问，请加群或者使用 Issue 或 Discussions

<br>

## 阅读地址

图片直接上传到 github，加载慢/加载不了请自备上网工具。

没有上网工具可以尝试使用 [瓦特工具箱](https://steampp.net/) 或者尝试羽翼城大佬的 [Steamcommunity 302](https://www.dogfight360.com/blog/18682/) 工具，或者 [FastGitHub](https://github.com/WangGithubUser/FastGitHub)

<br>

建议访问 gitbook 获得更佳阅读体验：

[https://90135.gitbook.io/com3d2_simple_mod_guide_chinese](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese)

请别急着点开教程，往主页下面看看

<br>

或者你也可以直接点本仓库里的 .md 文件阅读，项目地址：

[https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese](https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese)

<br>

## AI工具

您可以尝试使用 Google 推出的 [NotebookLM](https://notebooklm.google/) 来向本仓库内的内容提问。

打开 NotebookLM 后，添加以下网站作为资料来源，然后您就可以询问 AI 一些关于制作 MOD 的问题。

AI 提供的内容未必准确，因此请自行辨别回答内容。

```
https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese
https://seesaawiki.jp/com3d2mod_wiki/
https://seesaawiki.jp/com3d2_kaizou_info/
https://github.com/luvoid/COM3D2-All-Bout-Bones
https://github.com/luvoid/Blender-CM3D2-Converter
https://github.com/90135/COM3D2_MOD_EDITOR
https://github.com/MeidoPromotionAssociation/MeidoSerialization
https://drive.google.com/file/d/1ShCt2J2TxHheTbLYIzdowTGaA0AtLdY6
https://docs.google.com/document/d/1ynhUG_JwypWVQejG_p7DfHfGhAnioQKnhf21K_jMNaQ
https://docs.google.com/document/d/15MvjRXnqEFcPf2f2Ts5uBq-TuMj7usWfuAlcdaMUdIY
https://github.com/Neerhom/COM3D2.ModLoader/wiki/Creating-.asset_bg-files
https://github.com/Pain-Brioche/COM3D2.MaidLoader
```


## 目录及说明

### 附录

附录中也有很多知识，别忘了。

- [Blender CM3D2 Converter 插件去除机翻](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/blender-cm3d2-converter-cha-jian-qu-chu-ji-fan)
- [.menu文件的组成](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/.menu-wen-jian-de-zu-cheng)
- [BepInEx.ScriptLoader 插件修复](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/bepinex.scriptloader-cha-jian-zhi-jiao-ben-xiu-fu)
- [Blender 内预览材质](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/blender-nei-yu-lan-cai-zhi)
- [Blender 形态键批量删除脚本](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/blender-xing-tai-jian-pi-liang-shan-chu-jiao-ben)
- [ShaderServant 和 MaterialEditor 的安装](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/shaderservant-he-materialeditor-de-an-zhuang)
- [网页机翻](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/wang-ye-ji-fan)
- [游戏内传输形态键](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/fu-lu/you-xi-nei-chuan-shu-xing-tai-jian)

### 90135

**新手入门请从这里学起**

基于案例教学，让你明白原理以及为什么要这么做（大概吧

我建议你完整阅读教程，因为案例教学会有很多知识点散落其中，比如：骨骼跟随原理、以任意姿势导出、为什么表面是黑的……

在学完本课程后可以去看 ymk 老师的移植教程，有很多技巧。

- 90135 的 COM3D2 简明 MOD 教程
  - [第 1 课，MOD 的组成和使用 AlwaysColorChangeEx 插件制作简易换色](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-1-ke-mod-de-zu-cheng-he-shi-yong-accex-cha-jian-zhi-zuo-jian-yi-huan-se)
  - [第 2 课，初次修改模型之简易修改袜子穿模](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-2-ke-chu-ci-xiu-gai-mo-xing-zhi-jian-yi-xiu-gai-wa-zi-chuan-mo)
  - [第 3 课，移植小物件之特定于妹抖的东西](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-3-ke-yi-zhi-xiao-wu-jian-zhi-te-ding-yu-mei-dou-de-dong-xi)
  - [第 4 课，用形态键或 .anm 让物品动起来](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-4-ke-yong-xing-tai-jian-huo-anm-rang-wu-pin-dong-qi-lai)
  - [第 5 课，做一双 HighHell 插件高跟鞋](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-5-ke-zuo-yi-shuang-highhell-cha-jian-gao-gen-xie)
  - [第 6 课，使用摇曳骨让物体有物理效果](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/90135/90135-de-com3d2-jian-ming-mod-jiao-cheng/di-6-ke-shi-yong-yao-ye-gu-rang-wu-ti-you-wu-li-xiao-guo)


### 流转的四季

- [移植脸参考教程By流转的四季](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/liu-zhuan-de-si-ji/yi-zhi-lian-can-kao-jiao-cheng-by-liu-zhuan-de-si-ji)

### 无名

- [发光及呼吸灯教程](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/fa-guang-ji-hu-xi-deng-jiao-cheng/fa-guang-ji-hu-xi-deng-jiao-cheng)
- [基础anm动画教程](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/ji-chu-anm-dong-hua-jiao-cheng/ji-chu-anm-dong-hua-jiao-cheng)
- [头发教程](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/tou-fa-jiao-cheng/tou-fa-jiao-cheng)
- [如何使物体绕自己中心旋转](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/ru-he-shi-wu-ti-rao-zi-ji-zhong-xin-xuan-zhuan/ru-he-shi-wu-ti-rao-zi-ji-zhong-xin-xuan-zhuan)
- [摇曳骨教程](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/yao-ye-gu-jiao-cheng/yao-ye-gu-jiao-cheng)
- [材质基础教程](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/cai-zhi-ji-chu-jiao-cheng/cai-zhi-ji-chu-jiao-cheng)
- [裹缩权重](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/wu-ming/guo-suo-quan-zhong/guo-suo-quan-zhong)

### ymk

- ymk移植教程
  - [Chapter1 简介&Fb配件](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/ymk/ymk-yi-zhi-jiao-cheng/chapter1-jian-jie-fb-pei-jian/chapter1-jian-jie-fb-pei-jian)
  - [Chapter2 头发](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/ymk/ymk-yi-zhi-jiao-cheng/chapter2-tou-fa/chapter2-tou-fa)
  - [Chapter3 服装](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/ymk/ymk-yi-zhi-jiao-cheng/chapter3-fu-zhuang/chapter3-fu-zhuang)
  - [Chapter4 mate与其他](https://90135.gitbook.io/com3d2_simple_mod_guide_chinese/jiao-cheng/ymk/ymk-yi-zhi-jiao-cheng/chapter4-mate-yu-qi-ta/chapter4-mate-yu-qi-ta)

<br>

## 交流群

欢迎提问，但请勿私信提问

 - ymk 老师的 QQ 群：1420125923
 - Discord 英文大群 [https://discord.gg/custommaid](https://discord.gg/custommaid)

<br>

## 用到的工具

### 上网工具

- 自备
 - 没有上网工具基本寸步难行

### MOD 工具

 - 90135 编写的全功能 MOD 制作工具 
   - 支持所有 mod 文件格式（.menu、.mate、.pmat、.col、.phy、.psk、.tex、.anm、.model）
   - [https://github.com/90135/COM3D2_MOD_EDITOR](https://github.com/90135/COM3D2_MOD_EDITOR)

 - English Modding Tools Pack 英文改装工具包 2024.2.20 版
   - 内含各种各样的工具，其中最重要的是 SybarisArcEditor.exe
   - 有几个工具会报毒，不放心可以改用 90135 编写的工具，但是剩下的工具还是要用的
   - 此包中的工具有些修改过，和 github 上的不太一样，建议使用此包
   - 几个下载连接内容相同
   - [https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese/tree/main/%E7%B4%A0%E6%9D%90%E5%8C%85](https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese/tree/main/%E7%B4%A0%E6%9D%90%E5%8C%85)
   - [https://discord.com/channels/297072643797155840/865160223118196737/871444415498031145](https://discord.com/channels/297072643797155840/865160223118196737/871444415498031145)
   - [https://www.mediafire.com/file/na4751q6nctytpx/%5BCOM3D2%5DEnglish_Mod_Tools_Pack_2.20.2024.zip/file](https://www.mediafire.com/file/na4751q6nctytpx/%5BCOM3D2%5DEnglish_Mod_Tools_Pack_2.20.2024.zip/file)

### Blender 相关

Blender 是一个开源的建模软件

 - Blender 
   - 推荐 3.3.21 版本
   - 如果你要编辑 lobody highpoly 体型那种高顶点数模型，用 3.3 太卡的话，可以用 2.39.18，但是会有一些 bug，所以编辑完后要导回 3.3.21 再进行一些处理
   - 3.6 版本也能勉强使用，不过会有一些 BUG，比如有的功能不显示
   - [https://download.blender.org/release/](https://download.blender.org/release/)
   - [https://download.blender.org/release/Blender3.3/blender-3.3.21-windows-x64.zip](https://download.blender.org/release/Blender3.3/blender-3.3.21-windows-x64.zip)
  
 - Blender COM3D2 Converter 插件（必备）
   - Blender 2.83 使用 luv.2021.03.20b 或 luv.2021.08.26a
   - Blender 2.93.18 使用 luv.2022.09.16
   - Blender 3.3.21/3.6 使用最新版 (luv.2023.09.23)
   - [https://github.com/luvoid/Blender-CM3D2-Converter/releases](https://github.com/luvoid/Blender-CM3D2-Converter/releases)

 - Blender COM3D2 Converter 插件 4.4 版本
   - vonLeebpl 移植的的适用于 Blender 4.4 的版本
   - [https://github.com/vonLeebpl/Blender-CM3D2-Converter](https://github.com/vonLeebpl/Blender-CM3D2-Converter)

 - Blender MMD 插件
   - 3.3.21 使用 2.8.1 版本
   - [https://github.com/UuuNyaa/blender_mmd_tools/releases](https://github.com/UuuNyaa/blender_mmd_tools/releases)

#### Blender 教程

 - 先看这个速成一下，时间比较短
   - [【Blender】爆肝两个月！拜托三连了！这绝对是全B站最用心的（没有之一）Blender 3D建模零基础入门公开教程，耗时千余小时开发！](https://www.bilibili.com/video/av355541649)

 - Blender 界面介绍
   - [【Blender基本功】视图控制](https://www.bilibili.com/video/av114047521458279)
   - [【Blender基本功】软件界面基础](https://www.bilibili.com/video/av113832639136668)
   - [【Blender基本功】视图叠加层](https://www.bilibili.com/video/av114047521458279)
   - [【Blender基本功】着色方式参数详解](https://www.bilibili.com/video/av114047521458279)

 - UV 和材质教程
   - [什么是UV展开](https://www.bilibili.com/video/av114639941798871/)
   - [026 UV基础及常用操作](https://www.bilibili.com/video/av385242165)
   - [Blender 2.8 UV贴图教学](https://www.bilibili.com/video/av926436454)
   - [【Blender】3分钟掌握UV原理丨如何直接给模型涂色丨系列教程第三集](https://www.bilibili.com/video/av950602700)
  
 - 如果你不懂什么是骨骼、权重，请先学一下这个视频（虽然是某种搬运 AI 中文配音） 
   - [骨骼初学者教程（小白友好）｜Blender教程](https://www.bilibili.com/video/av114523105136618/)

 - 比较系统的课
   - [B站最新最全的Blender4.1全套精讲课程！爆肝6个月！全免费观看！Blender 0基础入门课程这一套就够了！](https://www.bilibili.com/video/av112852916568795)
   - [blender3.3完全入门教程(全集100集多持续更新中！)](https://www.bilibili.com/video/av218387890)
 
 - 理解 Blender 中的对象
   - [【Blender基本功】数据块 —— 面向对象建模](https://www.bilibili.com/video/av114047521458279/)
   - [【Blender基本功】精准变换 —— 三维变换、游标、轴心、轴向、吸附、衰减编辑一集速通](https://www.bilibili.com/video/av114047521458279)

 - 辣椒酱系统课（需要深入 Blender 时看）
   - [【合集8.21已更新93话】Blender 2.9-3.4黑铁骑士Ⅱ系统零基础入门教程(持续更新+中文字幕+普通话+不敷衍+义务教育+案例+学习)](https://www.bilibili.com/video/av205836298)


### 游戏插件相关

 - COM Modular Installer，简称 CMI（基础插件包）
   - 如果你的游戏是纯净版，那么先安装 CMI，可以让你获得一个基本的插件环境。
   - 附带中文/英文 PDF 说明，可以让你大致了解插件都是做什么用的
   - [https://github.com/krypto5863/COM-Modular-Installer](https://github.com/krypto5863/COM-Modular-Installer)
  
 - AlwaysColorChangeEx，简称 AccEX （MOD 制作工具）
   - `Sybaris\UnityInjector\`
   - [https://github.com/90135/COM3D2_Simple_MOD_Guide_Chinese/tree/main/%E7%B4%A0%E6%9D%90%E5%8C%85](https://github.com/90135/COM3D2_Simple_MOD_Guide_Chinese/tree/main/%E7%B4%A0%E6%9D%90%E5%8C%85)

 - ShaderServant，简称 SS（额外着色器支持插件）
   - `BepInEx\plugins\`
   - 官方只带了 NprShader 着色器，需要 liltoon 等着色器请看教程安装
   - [安装教程](https://github.com/90135/COM3D2_Simple_MOD_Guide_Chinese/blob/main/%E9%99%84%E5%BD%95/ShaderServant%20%E5%92%8C%20MaterialEditor%20%E7%9A%84%E5%AE%89%E8%A3%85.md)
   - [https://github.com/krypto5863/COM3D2.ShaderServant](https://github.com/krypto5863/COM3D2.ShaderServant)

 - MaterialEditor，简称 ME（类似 AccEX 的工具，但支持额外着色器）
   - `BepInEx\plugins\`
   - [安装教程](https://github.com/90135/COM3D2_Simple_MOD_Guide_Chinese/blob/main/%E9%99%84%E5%BD%95/ShaderServant%20%E5%92%8C%20MaterialEditor%20%E7%9A%84%E5%AE%89%E8%A3%85.md)
   - [https://discord.com/channels/297072643797155840/736350853442699284/1318388889169166336](https://discord.com/channels/297072643797155840/736350853442699284/1318388889169166336)

 - PropMyItem，Inory-S 版（随时随地呼出物品菜单）
   - `BepInEx\plugins\`
   - 比 Sybaris 版性能更好
   - 此版本在在更换物品时会打印 menu 名到物品栏，更利于做 MOD
   - 此外此版本还添加了隐藏的 accVag 和 accAnl 栏，可以让你多出 2 个栏位来放东西（这是官方自带的栏位，所以是不会出问题的）
   - [https://github.com/InoryS/COM3D2.PropMyItem.Plugin](https://github.com/InoryS/COM3D2.PropMyItem.Plugin)

 - ModItemExplorer（类似 PropMyItem，随时随地呼出物品菜单）
   - `Sybaris\UnityInjector\`
   - https://github.com/kidonaru/COM3D2.ModItemExplorer.Plugin

 - ShapekeyMaster（用于在游戏内调整形态键）
   - `BepInEx\plugins\`
   - [https://github.com/krypto5863/COM3D2.ShapekeyMaster](https://github.com/krypto5863/COM3D2.ShapekeyMaster) 注意，如果用此插件添加了形态键，那么除非设置为忽略，否则其他插件也无法调整那个形态键。如有此需求可以使用下面的修改版。
   - [https://github.com/InoryS/COM3D2.ShapekeyMaster](https://github.com/InoryS/COM3D2.ShapekeyMaster)  此版本可以在条件不满足时跳过 Shapekey 处理，以便其他插件可以使用
 
 - ShapeAnimator（用于在游戏内调整形态键，老款）
   - `Sybaris\UnityInjector\`
   - [https://github.com/InoryS/COM3D2.ShapeAnimator.Plugin](https://github.com/InoryS/COM3D2.ShapeAnimator.Plugin)

 - LiveLink（使用此插件可以让你的游戏实时同步 Blender 中的姿态，做动画什么的可以实时预览，且可以实时发送网格变形什么的。）
   - `BepInEx\plugins\`
   - [https://github.com/luvoid/COM3D2.LiveLink](https://github.com/luvoid/COM3D2.LiveLink)
  
 - PartEdit（用于在游戏内操作骨骼）
   - `Sybaris\UnityInjector\`
   - 注意不要安装 0Harmony.dll，这个是 BepinEx 自带的
   - [https://github.com/customordermaid3d2/COM3D2.PartsEdit.Plugin](https://github.com/customordermaid3d2/COM3D2.PartsEdit.Plugin)

 - DynBoneEdit（用于编辑摇曳骨 .col .phy）
   - `Sybaris\UnityInjector\`
   - 依赖于 AlwaysColorChangeEx
   - [https://ux.getuploader.com/trzr_tool/download/26](https://ux.getuploader.com/trzr_tool/download/26)

 - ShiftClickExplorer（编辑模式“shift+左键”编辑模式物品转跳到 MOD 文件所在位置）
   - `BepInEx\plugins\`
   - [https://github.com/krypto5863/COM3D2.ShiftClickExplorer](https://github.com/krypto5863/COM3D2.ShiftClickExplorer)

 - HighHeel-InoryS 版（高跟鞋插件）
   - `BepInEx\plugins\`
   - [https://github.com/InoryS/COM3D2.HighHeel](https://github.com/InoryS/COM3D2.HighHeel)
 
 - BodyCategoryAdd（编辑模式添加身体类别，用于更换体型）
   - `Sybaris\UnityInjector\`
   - [https://ux.getuploader.com/cm3d2_k/download/88](https://ux.getuploader.com/cm3d2_k/download/88)

 - ATCC（用于调整物品附着点，在游戏内可视化调整物品附着点坐标，然后可以填到 menu 内固化）
   - `Sybaris\UnityInjector\`
   - [https://uu.getuploader.com/hatena37/download/109](https://uu.getuploader.com/hatena37/download/109)

 - 插件汉化
   - [COM3D2 插件中文翻译](https://github.com/90135/COM3D2_Plugin_Translate_Chinese)

 - motimoti 博客的插件收集
   - [https://motimoti3d.jp/blog-entry-590.html](https://motimoti3d.jp/blog-entry-590.html)
   - [https://motimoti3d.jp/blog-entry-37.html](https://motimoti3d.jp/blog-entry-37.html)
  
#### 错误处理插件
制作 MOD 时总会遇到错误，可以说是必装了

 - ExtendedErrorHandling（捕获错误，出现错误时不崩游戏，必装）
   - `BepInEx\plugins\`
   - [https://github.com/krypto5863/COM3D2.ExtendedErrorHandling](https://github.com/krypto5863/COM3D2.ExtendedErrorHandling)

 - CatchUnityEventExceptions（确保 Unity 订阅可以执行）
   - `BepInEx\plugins\`
   - 下载里面的 CatchUnityEventExceptions
   - [https://github.com/BepInEx/BepInEx.Utility](https://github.com/BepInEx/BepInEx.Utility)

 - SuppressGetTypesErrorsPatcher（确保反射失败不会崩溃）
   - `BepInEx\plugins\`
   - 下载里面的 SuppressGetTypesErrorsPatcher
   - [https://github.com/BepInEx/BepInEx.Utility](https://github.com/BepInEx/BepInEx.Utility)

### 妙妙工具

 - Everything（全盘秒搜工具，还能搜文件内容什么的）
   - 建议开机启动，不然还要等索引
   - [https://www.voidtools.com/zh-cn/](https://www.voidtools.com/zh-cn/)
    
 - Bandizip（解压软件）
   - [https://cn.bandisoft.com/bandizip/](https://cn.bandisoft.com/bandizip/)

 - ScreenToGif（动图制作软件）
   - [https://www.screentogif.com/](https://www.screentogif.com/)

 - Photopea（在线版 PhotoShop）
   - [https://www.photopea.com/](https://www.photopea.com/)

### 素材相关

 -  90135 的 MOD，可以当素材或者示例使用，可以随意修改分发
   - [https://mega.nz/folder/QRNxhRRZ#yuoWg3O9OLUHnOF1nwmLDw](https://mega.nz/folder/QRNxhRRZ#yuoWg3O9OLUHnOF1nwmLDw)

 - InoryS 的 MOD，可以当素材或者示例使用，可以随意修改分发（已获同意）
   - [https://mega.nz/folder/U6Jy0a6a#Pv5G9G_J5zoYc46TVmz6iA](https://mega.nz/folder/U6Jy0a6a#Pv5G9G_J5zoYc46TVmz6iA)

### 条款相关

  - KISS 条款
   - 发布 MOD 时需要遵守此条款，还需要在 MOD 内放置条款链接
   - [中文版](https://com3d2.jp/zh/cn/terms.html)
   - [日文版](http://kisskiss.tv/kiss/diary.php?no=558)
   - [英文版](https://com3d2.world/r18/modrul.html)

## MOD 发布
一般发布在 Twitter 上，带 #COM3D2 标签

这几个站点会爬取 Twitter 推文，有的站不会收录有裸露图像的推文，比如 motimoti。

相对来说 mukuu.herokuapp.com 会全一些，不过我比较喜欢 motimoti3d.jp，motimoti3d 还有插件收集等内容。

   - [https://motimoti3d.jp/](https://motimoti3d.jp/)
   - [https://mods.meido.dev/](https://mods.meido.dev/)
   - [https://mukuu.herokuapp.com](https://mukuu.herokuapp.com)

## 其他参考资料
 - [https://seesaawiki.jp/com3d2mod_wiki/](https://seesaawiki.jp/com3d2mod_wiki/)
 - [https://seesaawiki.jp/com3d2_kaizou_info/](https://seesaawiki.jp/com3d2_kaizou_info/)
 - [https://discord.gg/custommaid](https://discord.gg/custommaid)
 - [https://github.com/luvoid/COM3D2-All-Bout-Bones](https://github.com/luvoid/COM3D2-All-Bout-Bones)

## 其他可以参考的教程

 - [ymk移植教程-图文(已整合到本仓库)含mod](https://drive.google.com/drive/folders/1o0k9QREGKRmVlEr5WZSNUvBd7773ulN-)
 - [zaj2001杂酱教程-视频](https://www.bilibili.com/video/BV1tu411R7TV/)
 - [BDFFZI教程-图文](https://bdffzi-blog.netlify.app/posts/2633666094)
 - [BDFFZI教程-视频](https://www.bilibili.com/video/BV148411i7uy/)
 - [zoobot移植教程备份-图文（英文/不完整）](https://github.com/AbsoluteOmega/How-to-port-character-model-to-COM3D2)
 - [DA教程-图文（图片简介中有网盘）](https://www.pixiv.net/artworks/105077558)

## 也可以看看其他仓库

- [COM3D2 中文 MOD 教程](https://github.com/MeidoPromotionAssociation/COM3D2_Simple_MOD_Guide_Chinese)
- [COM3D2 MOD 编辑器](https://github.com/90135/COM3D2_MOD_EDITOR)
- [COM3D2 插件中文翻译](https://github.com/90135/COM3D2_Plugin_Translate_Chinese)
- [90135 的 COM3D2 中文指北](https://github.com/90135/COM3D2_GUIDE_CHINESE)
- [90135 的 COM3D2 脚本收藏集](https://github.com/90135/COM3D2_Scripts_901)
- [90135 的 COM3D2 工具](https://github.com/90135/COM3D2_Tools_901)


# 许可证

[![CC BY-NC-ND 4.0](https://licensebuttons.net/l/by-nc-nd/4.0/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

本仓库的文本内容采用 [知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](https://mirrors.creativecommons.org/presskit/buttons/88x31/png/by-nc-sa.png) 进行许可，特别注明除外。

若您选择转载本仓库内容，则必须在文档开头等明显位置署名，署名必须包含标题、作者、被转载页面的网页链接，否则许可无效。

其他类型作品版权归原作者所有，如有作者授权则遵照授权协议使用。无作者授权协议情况下，以评论，报道为目的，遵照 Fair use（合理使用） 原则使用，并标注来源。

```
您可以自由地：
共享 — 在任何媒介以任何形式复制、发行本作品
演绎 — 修改、转换或以本作品为基础进行创作
只要你遵守许可协议条款，许可人就无法收回你的这些权利。

惟须遵守下列条件：
署名 — 您必须给出 适当的署名 ，提供指向本许可协议的链接，同时 标明是否（对原始作品）作了修改 。您可以用任何合理的方式来署名，但是不得以任何方式暗示许可人为您或您的使用背书。
非商业性使用 — 您不得将本作品用于 商业目的 。
相同方式共享 — 如果您再混合、转换或者基于本作品进行创作，您必须基于 与原先许可协议相同的许可协议 分发您贡献的作品。
没有附加限制 — 您不得适用法律术语或者 技术措施 从而限制其他人做许可协议允许的事情。

声明：
您不必因为公共领域的作品要素而遵守许可协议，或者您的使用被可适用的 例外或限制 所允许。
不提供担保。许可协议可能不会给与您意图使用的所必须的所有许可。例如，其他权利比如 形象权、隐私权或人格权 可能限制您如何使用作品。
```
