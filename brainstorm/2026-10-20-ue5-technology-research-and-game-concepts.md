# UE5 技术调研与游戏项目选型报告

> 调研与归档日期：2026-10-20
>
> 阶段：技术调研、头脑风暴与项目选型
>
> 范围：33 项 UE5 技术、12 个候选游戏项目
>
> 状态：讨论稿；未确定游戏类型、开发周期、预算或目标平台，未开始实现

## 目录

- [1. 研究目标与主要结论](#1-研究目标与主要结论)
- [2. 证据边界与版本说明](#2-证据边界与版本说明)
- [3. 33 项技术逐项调研](#3-33-项技术逐项调研)
  - [3.1 渲染、光照和材质](#31-渲染光照和材质)
  - [3.2 世界组织与内容生成](#32-世界组织与内容生成)
  - [3.3 角色、动画与变形](#33-角色动画与变形)
  - [3.4 物理、人群、网络、音频与推理](#34-物理人群网络音频与推理)
  - [3.5 制作和交付流程](#35-制作和交付流程)
- [4. 12 个候选游戏项目](#4-12-个候选游戏项目)
- [5. 项目之间的取舍与首选建议](#5-项目之间的取舍与首选建议)
- [6. 共用验证原则与待确认事项](#6-共用验证原则与待确认事项)
- [附录：技术与项目覆盖关系](#附录技术与项目覆盖关系)

## 1. 研究目标与主要结论

本项目希望做出规则简明、易上手、具有一定规模的游戏，同时通过少数重点方向深入实践 UE5 技术。核心目的不是堆砌功能，而是通过完整玩法持续检验技术的效果、成本和边界。

“易上手”应限制玩家需要理解的规则数量，而不是把项目缩成一个小场景。项目规模可以来自多个场景、任务变化、系统互动，以及完整的失败、重试和成长循环。技术深度集中在每个项目的 2～3 个重点上。

本报告整理了 12 个差异明显的候选方向。综合考虑可玩性、UE5 技术验证价值和范围控制，当前首选是 **G2「夜港潜入」**；如果更重视角色动作和底层玩法系统，则优先考虑 **G1「垂直街区跑酷追猎」**；如果更重视物理与环境系统互动，则考虑 **G3「工业灾区救援」**。

这些建议是供共同选型的研究判断，不是已经作出的开发承诺。

## 2. 证据边界与版本说明

### 2.1 已确认与尚未确认的内容

- 调研时，本地 UE 源码目录的 `Engine/Build/Build.version` 标记为 **5.8.3**。这证明源码目录的版本标识；调研没有启动引擎，不能据此宣称某个功能已在实际环境运行通过。
- 官方资料确认：**PCG、Substrate 在 5.7 宣布 Production-Ready；MegaLights、Movie Render Graph、Dataflow 在 5.8 宣布 Production-Ready**。这些声明不能推广到它们的每个新增子功能。[5.7 官方发布说明](https://www.unrealengine.com/news/unreal-engine-5-7-is-now-available)、[5.8 官方发布说明](https://www.unrealengine.com/news/unreal-engine-5-8-is-now-available)
- **Iris 存在状态信息冲突**：本地插件描述标记为 Beta，官方介绍页仍提示 Experimental。本报告保留差异，不擅自判定哪个代表实际运行状态。[Iris 官方介绍](https://dev.epicgames.com/documentation/unreal-engine/introduction-to-iris-in-unreal-engine)
- 本地插件描述中的标记、官方发布说明和具体运行结果是不同层级的证据。插件未设置 Experimental/Beta 标记，也不能证明其所有子功能均可直接用于交付。
- 本报告引用的是调研当时检索到的资料。官方页面可能继续更新，正式采用某项能力前，应以选定引擎版本的文档、插件配置和运行验证为准。

### 2.2 示例和验收指标的含义

- **工程**：公开提供的示例工程或项目。公开可获取不等于已在本地下载、迁移或验证。
- **内置示例/模板**：引擎或插件中的示例内容，需要核实实际版本是否包含以及如何启用。
- **演示**：可能只有视频和技术分享，不代表完整源码、素材或当前版本兼容性已经公开。
- **工具/素材/文档**：可以支持研究，但不是完整可玩游戏。
- 下文的验收数字均为**建议研究目标**，尚未实测，也不是 Epic 的性能保证。
- 本轮调查未修改游戏项目、未创建 UE 工程、未下载或安装软件，也未开始实现。本文档是研究结果的归档。

## 3. 33 项技术逐项调研

### 3.1 渲染、光照和材质

| 编号与技术 | 研究价值、边界与具体测量方向 | 已找到的项目或 Demo |
|---|---|---|
| **01. Nanite** | 研究高几何密度环境在真实游戏镜头下的成本。测几何密度、材质复杂度、实例数量变化时的 GPU 时间和显存；不能把它理解为“多边形从此没有成本”。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/nanite-virtualized-geometry-in-unreal-engine) | **City Sample，工程**：适合研究复杂城市资产与其他渲染系统共同工作的情况。[项目介绍](https://dev.epicgames.com/documentation/unreal-engine/city-sample-project-unreal-engine-demonstration) |
| **02. Nanite Foliage** | 当前按 **Experimental** 对待。重点是密集植物的几何表达、动画、远景稳定性和流送；并非给传统树叶网格勾选 Nanite 就完成了新管线。测近远景转换、风动成本、植被密度与显存。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/nanite-foliage) | **The Witcher 4 UE5 Tech Demo，演示**。可参考技术方向，不能视为公开可直接复用的完整游戏工程。[官方介绍](https://www.unrealengine.com/news/all-the-big-news-and-announcements-from-the-state-of-unreal-2025) |
| **03. Lumen** | 适合动态开关灯、昼夜变化、可改变环境的游戏。重点测室内外切换、间接光更新延迟、漏光、反射稳定性，以及不同追踪路径的成本。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/lumen-technical-details-in-unreal-engine) | **City Sample、Valley of the Ancient，工程**。后者是较早期示例，迁移成本需要另验。[Valley 项目](https://dev.epicgames.com/documentation/unreal-engine/valley-of-the-ancient-sample-game-for-unreal-engine) |
| **04. Lumen Lite** | 5.8 发布说明标为 **Beta**。它提供较低成本的中等质量路径，值得研究哪些动态光照变化能够保留、哪些反射和遮挡细节需要牺牲。应在同一场景比较画质与 GPU 时间。[发布说明](https://dev.epicgames.com/documentation/unreal-engine/unreal-engine-5-8-release-notes)、[性能指南](https://dev.epicgames.com/documentation/unreal-engine/lumen-performance-guide-for-unreal-engine) | **未核实到独立、专门展示 Lite 的可下载游戏工程**。可以将已有 Lumen 场景作为之后的对照对象，但不能声称旧示例已经使用 Lite。 |
| **05. MegaLights** | 5.8 宣布 Production-Ready。适合大量动态投影灯，但重叠灯、遮挡、采样噪声和降噪仍需验证。测灯光数量、覆盖范围、运动速度与画质/耗时的关系。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/megalights-in-unreal-engine) | **Unreal Fest Seattle 2024 MegaLights 演示**展示大量动态投影光源；本轮未确认独立完整工程可下载。[官方演示介绍](https://www.unrealengine.com/blog/catch-up-on-the-big-news-from-unreal-fest-seattle-2024) |
| **06. Virtual Shadow Maps（VSM）** | 深度在缓存效率和失效原因：移动物体、植被变形、灯光运动都可能改变成本。测缓存失效、阴影页使用量和最坏镜头下的 GPU 时间。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/virtual-shadow-maps-in-unreal-engine) | **Fortnite Battle Royale Chapter 4，已发布游戏案例**，有专门的 VSM 技术拆解。[官方技术文章](https://www.unrealengine.com/tech-blog/virtual-shadow-maps-in-fortnite-battle-royale-chapter-4) |
| **07. Temporal Super Resolution（TSR）** | 不只比较静态截图清晰度。应重点观察快速转向、细线、树叶、透明效果、新显露区域的残影与闪烁，并比较内部渲染分辨率、GPU 时间和运动画质。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/temporal-super-resolution-in-unreal-engine) | **City Sample，工程**，可作为复杂场景下的时域重建研究对象。[项目介绍](https://dev.epicgames.com/documentation/unreal-engine/city-sample-project-unreal-engine-demonstration) |
| **08. Substrate** | 适合湿润、涂层、金属、布料等具有辨识度的材质变化。研究材质层数、混合方式、光照响应与成本，避免只做材质展厅。不同 GBuffer 路径及可选子功能需分别核验。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/overview-of-substrate-materials-in-unreal-engine) | **Electric Dreams，工程**：同时展示 PCG 环境和 Substrate 材质。[官方项目](https://www.unrealengine.com/electric-dreams-environment) |

### 3.2 世界组织与内容生成

| 编号与技术 | 研究价值、边界与具体测量方向 | 已找到的项目或 Demo |
|---|---|---|
| **09. World Partition / OFPA / Data Layers** | 三者分别主要解决空间流送、编辑协作文件组织、内容分层与状态切换。应分别测跨区移动的卡顿/内存、协作冲突，以及任务状态切换正确性；Data Layers 本身不是完整存档系统。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/world-partition-in-unreal-engine) | **City Sample、Valley of the Ancient，工程**，后者包含世界状态和 Data Layers 的实际使用。[Valley 项目](https://dev.epicgames.com/documentation/unreal-engine/valley-of-the-ancient-sample-game-for-unreal-engine) |
| **10. Large World Coordinates（LWC）** | 官方概览仍提示 Beta。核心是远离原点后，各子系统的数据精度与转换是否正确。可将同一测试场景移到不同坐标测试，不必为了验证它制造一个巨大空地图。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/large-world-coordinates-in-unreal-engine-5) | 本轮未确认专门隔离验证 LWC 的可下载玩法 Demo。可靠起点是官方的转换和渲染指南，而不是仅凭“大地图”推断已解决精度问题。[转换指南](https://dev.epicgames.com/documentation/unreal-engine/large-world-coordinates-project-conversion-guidelines-in-unreal-engine-5) |
| **11. Procedural Content Generation（PCG）** | 深度在可控制、可复现和可迭代的生成。测固定种子复现、局部修改后的重生成时间、道路可达性和任务点合法性。生成出场景，不等于生成出可玩的关卡。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/procedural-content-generation-framework-in-unreal-engine) | **Electric Dreams、City Sample PCG，工程**。新版 City Sample PCG 使用引擎内部 PCG；原版城市生成主要依赖 Houdini，二者不能混称。[City Sample PCG](https://dev.epicgames.com/documentation/unreal-engine/city-sample-pcg-for-unreal-engine) |
| **12. Mesh Terrain** | **Experimental**。真正的三维网格地形适合洞穴、悬挑和复杂道路。值得验证编辑后碰撞、导航、流送和 PCG 的一致性；不能直接推导出“任意运行时挖洞/破坏地形”。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/mesh-terrain-in-unreal-engine) | **City Sample PCG，工程**，包含 Mesh Terrain 的实际应用。[项目说明](https://dev.epicgames.com/documentation/unreal-engine/city-sample-pcg-for-unreal-engine) |
| **13. Procedural Vegetation Editor（PVE）** | **Experimental**，用于植物资产制作。测变体制作效率、骨骼和几何输出、人工调整保留情况。它不能直接等同于运行时生态模拟。官方明确说明 **5.7 PVE 资产不能在 5.8 打开**。[技术文档与兼容性说明](https://dev.epicgames.com/documentation/unreal-engine/procedural-vegetation-editor-in-unreal-engine) | 官方 PVE 工作流和 **Quixel Megaplants，工具示例/素材**。适合作为植物制作起点，并非现成生态游戏。[资产集合](https://www.fab.com/sellers/Quixel%20Megaplants) |
| **14. Fast Geometry Streaming** | **Experimental**。针对静态、非玩法几何的快速流送。研究高速移动时的加载延迟、常驻内存和卡顿；不能把它当作所有 Actor、AI、物理状态的通用流送替代品。[插件文档](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/FastGeoStreaming) | **The Witcher 4 UE5 Tech Demo，演示**，官方明确提到使用该技术。[5.8 官方说明](https://www.unrealengine.com/news/unreal-engine-5-8-is-now-available) |

**选样区别：City Sample PCG 没有直接包含原版 City Sample 的整套 Mass 人群、交通与信号灯玩法系统。** 研究城市生成应看前者，研究人群交通应看后者。[官方差异说明](https://dev.epicgames.com/documentation/unreal-engine/city-sample-pcg-for-unreal-engine)

### 3.3 角色、动画与变形

| 编号与技术 | 研究价值、边界与具体测量方向 | 已找到的项目或 Demo |
|---|---|---|
| **15. Motion Matching / Pose Search** | 核心是从动画数据中选择适合当前姿势和运动意图的片段，不会凭空补全缺失动作。测急停转向、脚滑、轨迹响应、检索耗时和数据库大小。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/motion-matching-in-unreal-engine) | **Game Animation Sample，工程**，是最直接的研究基础。[官方项目](https://dev.epicgames.com/documentation/unreal-engine/game-animation-sample-project-in-unreal-engine) |
| **16. IK Rig / IK Retargeter** | 研究不同体型能否共享动作，以及手脚接触是否可靠。测不同身体比例、坡面、抓握目标上的接触误差和人工修正成本。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine) | **Game Animation Sample 的角色接入及 MetaHuman 接入流程**。[官方示例](https://dev.epicgames.com/documentation/unreal-engine/adding-a-metahuman-to-the-game-animation-sample-project-in-unreal-engine) |
| **17. Motion Warping / Pose Warping** | 前者主要调整根运动，使动作到达目标；后者调整朝向、步幅、坡面等姿态。测不同距离、角度和高度下的终点误差、拉伸与穿插。本地 Motion Warping 描述标记 Beta。[Motion Warping](https://dev.epicgames.com/documentation/unreal-engine/motion-warping-in-unreal-engine)、[Pose Warping](https://dev.epicgames.com/documentation/unreal-engine/pose-warping-in-unreal-engine) | **Valley of the Ancient、Game Animation Sample，工程**，分别提供目标对齐和姿态适配的参考。 |
| **18. Control Rig** | 研究脚部落地、双手持物、目光和交互接触，以及动画与程序控制的计算顺序和控制权。测接触误差、切换连续性、运行成本。新增物理/Dynamics/DMC 功能应单独判断状态。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/control-rig-in-unreal-engine) | **Zebra Sample，角色工程**，包含身体、面部 Rig 和示例动画；不是完整游戏。[Epic 示例](https://www.fab.com/listings/8bff9602-7087-4d86-b680-f55b263e8382) |
| **19. Unreal Animation Framework（UAF）** | **Experimental**。研究动画组织、数据流和复杂角色计算的方式。适合用同一角色行为与现有动画方案对比维护复杂度、性能和调试能力，不适合成为第一天就必须重写整个项目的前提。[插件文档](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/UAF) | **The Witcher 4 技术演示的 UAF 专题分享**，属于实现讲解。[官方技术分享](https://dev.epicgames.com/community/learning/talks-and-demos/RmO5/unreal-animation-framework-in-the-witcher-4-unreal-engine-5-tech-demo-unreal-fest-orlando-2025) |
| **20. ML Deformer** | 用训练数据学习形变修正，不负责生成角色动作。真正难点是高质量参考形变数据。应在未参与训练的动作上，与普通蒙皮比较误差、视觉收益和推理成本。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/ml-deformer-framework-in-unreal-engine) | **ML Deformer Sample，工程**，适合研究完整数据与运行链路。[Epic 示例](https://www.fab.com/listings/4c1f2eee-3004-4466-8c86-796e2e94d562) |
| **21. MetaHuman** | 适合近距离角色表现、对话和人群层级研究。分别测面部表现、身体动作衔接、LOD 变化和多角色预算；MetaHuman Collections 等新功能不能代表整个 MetaHuman 的成熟度。[人群文档](https://dev.epicgames.com/documentation/metahuman/metahuman-crowds-in-unreal-engine) | **Game Animation Sample 的 MetaHuman 接入**；另有 **MetaHuman Crowds Sample**，其新增人群工作流涉及实验性能力。[人群示例入口](https://dev.epicgames.com/documentation/metahuman/create-metahuman-crowds-in-unreal-engine) |

Game Animation Sample 的 5.7 更新已展示 Mover 等内容，但文章中提到的未来 UAF 计划，不能当作“当前工程已经全面切换 UAF”的证据。[5.7 更新说明](https://www.unrealengine.com/tech-blog/explore-the-updates-to-the-game-animation-sample-project-in-ue-5-7)

### 3.4 物理、人群、网络、音频与推理

| 编号与技术 | 研究价值、边界与具体测量方向 | 已找到的项目或 Demo |
|---|---|---|
| **22. Chaos / Dataflow** | Chaos 涉及模拟；Dataflow 主要组织物理资产的制作与处理流程。研究可控破坏、连接关系、碎片休眠、碰撞和导航恢复。测活跃碎片数量、CPU 时间和结果稳定性，不能把 Dataflow 当作另一个物理求解器。[破坏工作流](https://dev.epicgames.com/documentation/unreal-engine/dataflow-for-destruction-quickstart) | **Valley of the Ancient、City Sample，工程**；另有官方 Dataflow 破坏快速入门。 |
| **23. Niagara Fluids** | 本地插件描述标记 Beta。研究烟、火、气体的表达和预算；官方区分偏游戏用途的 2D 模板与偏影视用途的 3D 模板。测分辨率、GPU 时间、可读性及烘焙替代方案。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/niagara-fluids-in-unreal-engine) | **Niagara Fluids Content Examples Hallway、插件模板，示例内容**。[官方示例讲解](https://dev.epicgames.com/community/learning/tutorials/lE4R/unreal-engine-the-niagara-fluids-content-examples-hallway) |
| **24. MassEntity / MassGameplay** | MassEntity 提供面向数据的实体组织；MassGameplay 补充表现、生成、LOD、状态等能力。本地 MassGameplay、MassAI、MassCrowd 描述标记 Experimental。研究数量扩展、行为更新频率和远近表现切换，而非只展示大量静止人形。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/overview-of-mass-gameplay-in-unreal-engine) | **原版 City Sample，人群/交通工程案例**。不能用 City Sample PCG 直接代替它。 |
| **25. Iris** | 研究复制过滤、优先级、带宽和大量状态同步；它不自动解决游戏逻辑一致性和运动回滚。测网络条件变化下的状态延迟、带宽与错误。本地 Beta、官方介绍 Experimental 的差异仍待运行版本核实。[官方介绍](https://dev.epicgames.com/documentation/unreal-engine/introduction-to-iris-in-unreal-engine) | **Lyra 的可选 Iris 接入/升级参考**，需要按对应版本确认配置。[官方升级说明](https://dev.epicgames.com/documentation/unreal-engine/upgrading-the-lyra-starter-game-to-the-latest-engine-release-in-unreal-engine) |
| **26. Mover** | **Experimental**。适合研究可组合运动、预测和回滚，但接触、交互、动画与网络的衔接仍是项目责任。测延迟、丢包、运动模式切换和修正幅度。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/mover-in-unreal-engine) | **Mover Examples，内置示例**；Game Animation Sample 5.7 也提供研究入口。官方明确提醒示例不应被直接当作可交付实现。[示例文档](https://dev.epicgames.com/documentation/unreal-engine/mover-examples-in-unreal-engine) |
| **27. MetaSounds** | 适合与速度、压力、材质、机械状态联动的程序音频。测状态表达是否可辨、声音数量和音频线程成本。音频内部的精确调度不等于游戏输入到扬声器零延迟。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/metasounds-the-next-generation-sound-sources-in-unreal-engine) | **Valley of the Ancient、Lyra，工程**，可研究参数驱动和游戏事件关联。 |
| **28. Neural Network Engine（NNE）** | 官方概览标记 **Beta**，不同运行后端状态不同。NNE 负责模型推理接入，不负责自动训练出好用的游戏 AI。测模型算子支持、推理延迟、线程同步、内存和失败回退。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/neural-network-engine-overview-in-unreal-engine) | **NVIDIA NNE-TensorRT-Sample，源码工程**，展示风格迁移推理；其 UE5.7、Windows/DX12、RTX 等要求不能推广成通用兼容性，也不证明智能 NPC 已经现成可用。[项目源码](https://github.com/NVIDIA-RTX/NNE-TensorRT-Sample) |

### 3.5 制作和交付流程

| 编号与技术 | 研究价值、边界与具体测量方向 | 已找到的项目或 Demo |
|---|---|---|
| **29. Movie Render Graph（MRG）** | 5.8 宣布 Production-Ready。适合统一输出角色、材质、镜头对照和宣传片。测批量配置、输出一致性、渲染失败恢复与人工操作量；离线高质量输出不能代表游戏实时性能。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/transitioning-to-the-movie-render-graph-from-movie-render-queue-in-unreal-engine) | **Meerkat Sample 的 MRG 示例**，上述官方文档明确提供参考。 |
| **30. Zen / UBA / Horde** | 分别涉及缓存/数据、构建加速、构建和测试编排。适合研究项目迭代效率。测冷/热缓存时间、增量构建、失败恢复；没有多机资源时不预设分布式构建一定划算。[Zen 文档](https://dev.epicgames.com/documentation/unreal-engine/zen-storage-server-for-unreal-engine)、[Horde 文档](https://dev.epicgames.com/documentation/unreal-engine/horde-in-unreal-engine) | **Horde 的 Lyra 构建、打包和自动测试教程**，属于工作流示例，不是另一种游戏玩法。[官方教程](https://dev.epicgames.com/documentation/unreal-engine/horde-test-automation-tutorial-for-unreal-engine) |
| **31. PSO Precaching** | 研究第一次遇到材质、效果或场景时的卡顿。重点测缺失/过晚 PSO、首次出现的慢帧和缓存覆盖；必须看打包程序与冷缓存情形，编辑器里跑过一次不能算通过。[技术文档](https://dev.epicgames.com/documentation/unreal-engine/pso-precaching-for-unreal-engine) | 主要参考是官方验证与缓存工作流。本轮未确认专门隔离展示此技术的独立可玩工程；适合纳入所有候选项目的交付验证。 |
| **32. 移动渲染** | 需要从渲染路径、材质、光照、内存、输入和持续运行状态整体设计。普通移动渲染与手机上的桌面渲染路径不能混为一谈，也不能默认 Lumen Lite 等同于普遍手机支持。[功能表](https://dev.epicgames.com/documentation/unreal-engine/mobile-rendering-features-in-unreal-engine) | **更新版 Stack-O-Bot，工程**，官方以高端 PC 到移动设备的适配作为示例目标之一。[官方介绍](https://www.unrealengine.com/news/updated-stack-o-bot-for-unreal-engine-5-6-available-now) |
| **33. Unreal MCP / Sandboxes** | 按 **Experimental** 对待。适合研究编辑操作的可重复性、变更范围、隔离与合并。测指令完成率、误改资产数量和恢复能力；不能把编辑隔离当成操作系统安全沙箱，也不能直接等同于游戏 NPC 智能。[Unreal MCP](https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor)、[SandboxedEditing](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/SandboxedEditing) | **City Sample PCG 的 Edits 示例与 PCG＋LLM 工作流**提供实际制作案例。[官方工作流](https://dev.epicgames.com/documentation/unreal-engine/working-with-pcg-and-llms-using-unreal-mcp-in-unreal-engine) |

这 33 项并不处于同一层面。**MRG、Zen/UBA/Horde、PSO、MCP/Sandboxes 可以服务多个游戏项目，不需要为了覆盖它们硬造一种玩法。**

## 4. 12 个候选游戏项目

以下规模是供选型讨论的内容边界。每个方案都能形成完整游戏循环，但尚不意味着批准开发这些数量的内容。

### G1｜垂直街区跑酷追猎

**类型：第三人称动作 / 路线规划**

一句话玩法：在立体街区选择路线，穿越障碍和巡逻区域，完成运送或追踪任务。

- **核心循环与规模**：接任务 → 观察路线 → 穿越/躲避追踪 → 完成目标 → 用更熟练的路线重试。建议 4 个互联街区、3 类任务、8～12 个任务配置、2 类巡逻者。
- **主研究技术**：Motion Matching、Motion/Pose Warping、Control Rig。辅助研究 IK 重定向、World Partition、TSR；Mover、UAF 放入独立对照实验。
- **具体验证**：200 次合法翻越/跨越组合中成功率目标 ≥95%；落点误差 P95 ≤5 厘米；取消、受击、落地后无输入锁死。记录脚滑、检索耗时和动画数据规模。
- **最大风险**：动画覆盖、接触修正、相机三者相互影响。
- **明确不做**：完整城市生活、复杂枪战、车辆系统和庞大技能树。

### G2｜夜港潜入

**类型：系统潜行 / 小型盗取任务**

一句话玩法：利用灯光、电力和有限工具穿过警戒区，拿到目标并撤离。

- **核心循环与规模**：侦察 → 选择路线 → 改变照明/制造干扰 → 获取目标 → 应对警报并撤离。建议 3 个室内外结合的场所，每处 2～3 条主要路线、3 种工具、3 类安保机制，任务和警报状态可变化。
- **主研究技术**：Lumen、MegaLights、Substrate。辅助比较 VSM、TSR、Lumen Lite 和 Nanite 场景成本。
- **具体验证**：设置 100 组明暗/遮挡测试位置，人工标注的可见性与游戏曝光判定一致率目标 ≥95%；比较 32/64/128 个动态光源测试负载下的耗时、噪声和反射稳定性。潜行判定使用明确的游戏规则，再验证它与视觉表现一致。
- **最大风险**：玩家眼中的明暗与 AI 判断不一致，会直接破坏公平感。
- **明确不做**：整座城市模拟、所有反射参与侦测、无限自由破坏。

### G3｜工业灾区救援

**类型：系统解题 / 环境操作**

一句话玩法：在事故现场切断危险源、打开通路，把被困人员带到安全区域。

- **核心循环与规模**：判断事故 → 断电/通风/破拆 → 控制危险 → 救出人员 → 撤离并复盘。建议 3 个工业区域、4 类危险、3 种主要工具、6～8 个任务。
- **主研究技术**：Chaos/Dataflow、Niagara Fluids、MetaSounds。灯光与材质主要服务危险识别。
- **具体验证**：固定操作序列重复 30 次，任务结果一致率目标 ≥90%；画质变化不能改变危险判定；测活跃碎片、流体分辨率、声音数量与帧时间。烟火视觉和决定伤害/可通行性的游戏状态分别实现。
- **最大风险**：破坏、导航、烟雾和救援 AI 容易相互牵连。
- **明确不做**：工程级火灾仿真、整座城市倒塌或完整联网流体模拟。

### G4｜林海巡护

**类型：区域探索 / 追踪与修复**

一句话玩法：穿越不同森林区域，根据环境线索完成追踪、修复和救援任务。

- **核心循环与规模**：准备装备 → 选路线 → 阅读痕迹/处理障碍 → 完成委托 → 返回并开放新路线。建议 3 个相连生态区、约 12 个关键地点、3 类任务和有限环境状态变化。
- **主研究技术**：PCG、Nanite Foliage、World Partition。PVE 研究资产制作；Mesh Terrain、FastGeo 作为可替换扩展。VSM、TSR 和常规 Nanite 场景可作为渲染对照。
- **具体验证**：50 个生成种子中，任务主路和必要物品可达率必须 100%；比较植被密度、风动、行进速度下的 GPU、内存和流送慢帧；测局部调整后的重生成时间。
- **最大风险**：多个实验性系统同时成为必需依赖。
- **明确不做**：无限生态、复杂生物繁殖链、重度生存制造或无限地图。

### G5｜枢纽疏散

**类型：人群管理 / 动态调度**

一句话玩法：通过开关通道、设置指引和调度救援资源，让拥挤人群安全离开危险区域。

- **核心循环与规模**：观察拥堵 → 调整通道和引导 → 观察人群反应 → 应对新危险 → 评估疏散结果。建议 3 套枢纽布局、3 类事故、6 个情境任务。
- **主研究技术**：MassEntity/MassGameplay、PCG、MetaSounds。MetaHuman 人群表现是可选扩展。
- **具体验证**：以 100/500/2000 个实体作为压力测试档位，记录 CPU/GPU、内存和远近表现切换成本；统计到达率、拥堵持续时间和死锁。数量只是测试负载，不预先承诺目标设备都能承受。
- **最大风险**：实体数量上去之后，局部避障、行为决策和表现切换可能失去一致性。
- **明确不做**：全城市经济、每个居民的复杂人生或交通大世界。

### G6｜3v3 街头动能球

**类型：简化规则的团队运动**

一句话玩法：通过跑位、传球、冲刺和射门，在紧凑场地中争夺球权并得分。

- **核心循环与规模**：争球 → 拉开空间 → 传递/突破 → 射门 → 攻防转换。建议 2 个球场、3 种角色特点，核心操作控制在移动、传球、射门、冲刺；包含 AI 对手和联网对照需求。
- **主研究技术**：Motion Matching、Iris、Mover。Motion Warping、IK 提供动作接触支持。
- **具体验证**：在 RTT 0/80/150 毫秒、丢包 0/1/3% 条件下，100 次球权转移无双重持有、无重复计分；记录位置修正、传球延迟和带宽。
- **最大风险**：身体动作、持球权威状态与网络预测耦合很深。应逐项比较 Iris、Mover，UAF 不同时加入主线。
- **明确不做**：完整职业体育规则、联赛经营和大型匹配服务。

### G7｜荒原运输

**类型：驾驶物流 / 路线与载荷管理**

一句话玩法：选择货物和路线，驾驶运输车穿过变化的道路，把货物完整送达。

- **核心循环与规模**：接单装货 → 规划路线 → 驾驶并处理道路障碍 → 交付 → 维修和重新配载。建议 1 条长主干路线、3 个站点、若干支路、3 类货物。
- **主研究技术**：World Partition、LWC、Fast Geometry Streaming。Chaos 负责车辆/载荷相关研究，MetaSounds 表达机械状态。Mesh Terrain、Nanite、TSR 和 Lumen Lite 可作为地形与渲染的独立扩展。
- **具体验证**：同一段测试道路分别放在距原点 0/10/100 公里处，比较抖动、接触误差和驾驶结果；在不同速度下测流送延迟与卡顿。用坐标偏移实验验证精度，不必实际制作百公里内容。
- **最大风险**：流送边界与物理状态衔接。
- **明确不做**：专业汽车仿真、庞大经济系统和空旷超大地图。

### G8｜双人格斗训练赛

**类型：少角色近身对抗**

一句话玩法：用少量攻击、防御、闪避和抓取动作，读取对手并寻找反击机会。

- **核心循环与规模**：试探 → 判断动作 → 进攻/防守/反制 → 赢下一回合 → 调整策略。建议 2 个角色、3 个场地、每个角色约 10～12 个核心动作。
- **主研究技术**：Control Rig、Motion Warping、ML Deformer。辅助研究 Pose Search 和皮肤/服装材质；MRG 可用于固定镜头下的形变和材质对照。
- **具体验证**：不同身体比例和相对距离下，接触动作误差可控；ML Deformer 必须在未见过的姿势上优于普通蒙皮，并报告额外耗时，而不是只展示训练动作效果。
- **最大风险**：高质量动作和参考形变数据的制作成本，可能超过代码成本。
- **明确不做**：大量角色、超长连招、复杂竞技联网和海量服装。

### G9｜封闭街区调查

**类型：叙事调查 / 证据推理**

一句话玩法：在有限街区搜集线索、询问人物，通过证据矛盾还原事件。

- **核心循环与规模**：探索现场 → 与角色交谈 → 交叉核对证据 → 重建事件 → 作出结论。建议 3 个案件、6 名核心 NPC、4 个可重复利用的场所，采用有限分支对话。
- **主研究技术**：MetaHuman、Control Rig、Substrate。辅助研究 Lumen 和动作重定向，MRG 用于统一角色镜头对照。
- **具体验证**：对话中断、恢复、切换对象共 100 次，无相机、输入或角色控制权残留；记录近景角色数量、表情与身体衔接、LOD 切换的预算和突变。
- **最大风险**：剧本、表演和角色内容量成为主要工作。
- **明确不做**：无限自由聊天、自动生成整部剧情或巨大分支树。

### G10｜伙伴机器人试炼

**类型：合作解谜 / 学习型行为**

一句话玩法：指挥一个机器人伙伴协作搬运、按压和牵引，通过逐渐变化的空间谜题。

- **核心循环与规模**：观察问题 → 演示/发指令 → 伙伴执行 → 协同完成 → 在新布局中迁移经验。建议 1 个伙伴、3 类基础动作、约 10 个房间及布局变体。
- **主研究技术**：NNE、Control Rig；MetaSounds 用于明确表达伙伴状态。模型优先只负责高层动作选择，基础动作仍保持可验证。
- **具体验证**：在 100 个未参与训练的布局上，与规则 AI 比较成功率、完成时间和推理延迟；模型失败时能够回退，不让任务永久卡死。
- **最大风险**：数据、训练与泛化能力的不确定性高。现有 NNE 风格迁移 Demo 只证明推理接入方式，不能证明这个伙伴行为已经具备。
- **明确不做**：端到端自由学习一切或无限自主代理。

### G11｜地下遗迹远征

**类型：模块化动作冒险 / 轻量 Roguelite**

一句话玩法：在可变化的遗迹路线中战斗、利用环境并选择强化，争取完成一次远征。

- **核心循环与规模**：选择路线 → 战斗/环境交互 → 获得有限强化 → 面对阶段目标 → 撤离或重新挑战。建议 2 个区域主题、约 20 个房间模块、4 类敌人、3 种具有组合关系的能力。
- **主研究技术**：PCG、Niagara Fluids、MetaSounds。Chaos 支持局部环境破坏，PSO 关注首次出现的特效和材质。
- **具体验证**：100 个种子中主路、出口和必需目标可达率 100%；测不同能力组合下的特效预算与战斗可读性；记录生成耗时、导航更新和首次遭遇慢帧。
- **最大风险**：组合数量快速扩大，生成正确不代表战斗好玩。
- **明确不做**：无限技能、无限房间、开放世界生存制造。

### G12｜俯视角工厂防卫

**类型：部署与即时操作结合**

一句话玩法：在波次间布置装置，战斗时亲自移动、维修和处理防线缺口。

- **核心循环与规模**：部署 → 防守/维修 → 获取资源 → 调整配置 → 应对新组合。建议 1 个工厂据点、2 种区域主题、6 个阶段、3 类装置、5 类敌人。
- **主研究技术**：移动渲染、PSO Precaching、MetaSounds。PCG 可用于场景变体；需要大规模敌人时再评估 Mass。
- **具体验证**：在选定设备上进行冷缓存打包运行，记录 PSO 缺失、首次出现卡顿、内存峰值；持续运行 30 分钟观察性能变化，再比较画质档位和输入体验。
- **最大风险**：持续负载、发热、内存和材质变体。
- **明确不做**：把全部桌面渲染特性搬到手机。**这是移动方向的候选方案，不代表已确定整个项目的目标平台。**

## 5. 项目之间的取舍与首选建议

### 5.1 按研究兴趣选择

| 如果最想深挖的是…… | 优先项目 | 主要取舍 |
|---|---|---|
| 动态光照、材质、画面与玩法的一致性 | **G2 夜港潜入** | 技术与场景质量联系紧密，但必须把视觉和潜行规则校准 |
| 角色动作、接触、动作中断与控制权 | **G1 跑酷追猎** | 有很好的公开工程入口，动画覆盖和相机仍需投入 |
| 可破坏环境、多系统互动 | **G3 工业救援** | 玩法表达强，系统之间的依赖也最容易扩大 |
| 程序化世界、植被与流送 | **G4 林海巡护** | 自然覆盖大量新技术，但实验性依赖较多 |
| 大量实体与行为性能 | **G5 枢纽疏散** | 比单纯“摆几千个人”更能验证系统；行为正确性难 |
| 动作与网络状态一致性 | **G6 动能球** | 技术深度很高，调试和验证成本也高 |
| 远距离移动、精度和高速流送 | **G7 荒原运输** | 验证目标明确，必须避免内容规模失控 |
| 高质量角色形变 | **G8 格斗训练赛** | 数据和动画制作是主要门槛 |
| 数字人、表演和交互状态 | **G9 街区调查** | 更依赖剧本和表演内容 |
| 模型推理与游戏 AI 的实际收益 | **G10 机器人试炼** | 研究不确定性最高，不能预设机器学习一定胜过规则 |
| 生成关卡与可重复游玩 | **G11 遗迹远征** | 内容复用好，但组合测试量容易增长 |
| 设备适配与交付质量 | **G12 工厂防卫** | 研究价值在稳定运行和体验，而非单张画面上限 |

### 5.2 综合首选：G2「夜港潜入」

G2 的优势是，一套相对简单的玩家规则——观察、移动、干扰、拿取、撤离——就能自然产生对动态光照、阴影、反射、材质和性能的持续压力。多个场所、安保组合和警报状态能够形成实际规模，同时不必引入大世界、人海、网络预测等另一组复杂问题。


G3 则适合把“环境如何被玩家改变”作为核心研究主题。它比单独制作破坏或烟火展示更能暴露系统边界，但需要严格控制危险类型和可破坏范围。

## 6. 共用验证原则与待确认事项

### 6.1 共用验证原则

1. **每个项目保留 2～3 项主要研究技术。** 其他技术是支撑或独立对照项。
2. **每项技术都要有基线。** 相同内容、相同镜头或输入，比较启用前后的质量、成本和失败情况。
3. **性能结论覆盖运行过程。** 记录 CPU/GPU 时间、P95/P99 慢帧、内存、首次出现卡顿；目标硬件未定前，不先承诺统一帧率。P95/P99 用于观察较慢帧，而非只看平均帧率。
4. **实验性能力应允许替换。** 森林玩法可以先依赖常规植被成立，再比较 Nanite Foliage；运动玩法也应能单独比较 Mover 和现有运动方案。
5. **同时验收游戏正确性与技术收益。** 不能只以成功开启功能或录出一段漂亮视频为完成标准。
6. **制作效率与运行效果分别测量。** PCG、PVE、MCP、构建工具的收益应记录人工操作量、迭代时间和变更正确性，不能全部折算成运行帧率。
7. **不要同时引入多个未经验证的底层替换。** 例如在同一阶段把 Iris、Mover、UAF 全部替换到主线，会显著增加定位问题的难度。

### 6.2 尚待确认

- 选定引擎构建的实际运行兼容性，包括实验性插件之间的组合。
- 公开示例在当前引擎版本中的迁移和维护成本。
- 可合法使用的动画、美术、音频和训练数据，以及需要自行制作的部分。
- 目标设备、性能预算、输入方式和是否必须联网。
- 团队希望优先研究的技术方向，以及愿意承担的内容制作与实验性技术风险。

这些未知不影响本轮方案比较，但需要在选定方向后的验证阶段解决。当前没有把任何候选项目视为已经批准实施。

## 附录：技术与项目覆盖关系

下表包含主研究技术、辅助技术和可选实验，不代表一个项目必须同时启用该行或该列中的所有技术。

| 编号 | 技术 | 适合的候选项目或共用流程 |
|---|---|---|
| 01 | Nanite | G2、G4、G7 |
| 02 | Nanite Foliage | G4 |
| 03 | Lumen | G2、G3、G9 |
| 04 | Lumen Lite | G2、G7 |
| 05 | MegaLights | G2；G3 可选照明对照 |
| 06 | VSM | G1、G2、G4 的阴影对照 |
| 07 | TSR | G1、G2、G4、G7 |
| 08 | Substrate | G2、G8、G9 |
| 09 | World Partition / OFPA / Data Layers | G1、G4、G7；OFPA 按协作需要评估 |
| 10 | LWC | G7 |
| 11 | PCG | G4、G5、G11、G12 |
| 12 | Mesh Terrain | G4、G7 的可替换地形实验 |
| 13 | PVE | G4 的植物制作流程 |
| 14 | Fast Geometry Streaming | G4、G7 |
| 15 | Motion Matching / Pose Search | G1、G6、G8 |
| 16 | IK Rig / IK Retargeter | G1、G6、G9 |
| 17 | Motion Warping / Pose Warping | G1、G6、G8 |
| 18 | Control Rig | G1、G8、G9、G10 |
| 19 | UAF | G1、G6 的独立动画框架对照，不与其他底层替换同时加入 |
| 20 | ML Deformer | G8 |
| 21 | MetaHuman | G9；G5 可选人群表现 |
| 22 | Chaos / Dataflow | G3、G7、G11 |
| 23 | Niagara Fluids | G3、G11 |
| 24 | MassEntity / MassGameplay | G5；G12 按实体规模和平台兼容性评估 |
| 25 | Iris | G6 |
| 26 | Mover | G6；G1 独立运动对照 |
| 27 | MetaSounds | G3、G5、G7、G10、G11、G12 |
| 28 | NNE | G10 |
| 29 | MRG | G8、G9 的统一镜头输出；其他项目按制作需要采用 |
| 30 | Zen / UBA / Horde | 所有项目的可选缓存、构建和测试流程 |
| 31 | PSO Precaching | 所有项目的交付验证；G1、G11、G12 尤其适合关注首次出现卡顿 |
| 32 | 移动渲染 | G12；其他项目仅在目标平台明确后评估 |
| 33 | Unreal MCP / Sandboxes | 所有项目的可选制作流程；G4、G11 的 PCG 内容编辑尤其适合对照 |

覆盖检查：33 项技术均有研究落点，12 个游戏方向均包含玩法循环、建议规模、研究技术、验证目标、主要风险和明确不做的内容。
