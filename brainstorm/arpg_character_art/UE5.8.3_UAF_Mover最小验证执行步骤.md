# UE5.8.3 UAF Mover最小验证执行步骤

日期：2026年10月25日。负责人：程序侧。先发布本流程和[交接包清单](角色资产交接包清单.md)，再开始本机验证。设计、玩法和美术标准沿用策划侧文档；本流程中的诊断角色和测试动作只用于技术验证。

完整决策见[复盘意见与本地落地建议](复盘意见与本地落地建议.md)。不等待角色资产即可推进M0至M6；资产收到后才进入A0至A3。

## 当前状态

| 项目 | 状态 |
|---|---|
| 目标引擎 | 已核对本地Build.version为UE5.8.3 |
| 插件及参考资产 | 已发现UAF、Mover UAF、Mover Examples编译模块、UAF基础角色模板与Mover示例地图 |
| 实验工程 | 待创建，拟名ARPGMotionLab |
| 插件实际加载、运行与联动 | NOT RUN |
| 同事角色导入、画面、布料、性能与打包 | NOT RUN |

文件存在不等于插件已成功加载，更不等于UAF/Mover组合已经运行。

## M0 建立独立实验工程

1. 用UE5.8.3创建Blank蓝图工程ARPGMotionLab，不修改现有游戏工程。程序侧拟放在 `E:/ue5_workspace/projects/ARPGMotionLab`。
2. 记录引擎版本、项目版本、Windows版本、CPU、GPU、显存和测试分辨率。帧率目标暂不定；后续30/60/120限制只作为测试条件。
3. 启用UAF、Mover、Mover UAF、Mover Examples、Enhanced Input；编辑器自动化按需启用Python Editor Script Plugin与Editor Scripting Utilities。Motion Warping到M5再加入。
4. 重新打开该实验工程，检查插件缺失、模块不兼容和资源加载错误。首次启动若要求编译，先记录范围与错误，不升级或降级引擎来绕开问题。
5. 建立 `Content/Lab/Maps`、`Characters`、`Animation`、`Input`；运行证据保存在 `Saved/Validation/<run-id>`。
6. 保存并关闭，再重新打开，确认项目及设置可复现。

产物：可打开的实验工程、环境记录和启动日志。通过条件：所需模块成功加载，保存重开正常。失败时停在环境或模块层，后续运行项保持NOT RUN。

程序侧工程创建后，可用以下PowerShell入口启动；其他机器替换两个路径：

```powershell
$EngineRoot = 'E:\ue5_workspace\UE_5.8.3'
$LabProject = 'E:\ue5_workspace\projects\ARPGMotionLab\ARPGMotionLab.uproject'
& "$EngineRoot\Engine\Binaries\Win64\UnrealEditor.exe" $LabProject -log
```

## M1 建立Mover对照基线

1. 在内容浏览器显示插件内容，打开Mover Examples的 `L_CharacterMovementBasics`。
2. 按官方示例配置Network Prediction：Preferred Ticking Policy为Fixed、Simulated Proxy Network LOD为Interpolated、Enable Fixed Tick Smoothing为true。
3. PIE检查前后左右、斜向、起停、转向、坡道、台阶与墙体阻挡，记录操作和结果。
4. 查看AnimatedMannyPawn的组件、输入和动画来源，明确这份样例使用什么移动后端与姿态实现。
5. 保存一段基线录像和问题表。保持引擎自带示例只读，实验修改放进项目自己的资产。

通过条件：示例能实际被操控，移动与碰撞可重复。它是对照基线，不作为UAF主角验收。上述Fixed配置仅服务该示例，不自动继承到后续Standalone试验。[Epic Mover示例](https://dev.epicgames.com/documentation/en-us/unreal-engine/mover-examples-in-unreal-engine)

## M2 证明UAF确实产生最终姿态

1. 使用诊断人形网格和一段待机、一段移动动画；固定网格、骨架和动画版本。
2. 从本机UAF BasicCharacter模板建立项目副本。检查其资产引用、输入与输出配置，不直接覆盖模板。
3. 先只接待机，再接入速度驱动的待机与移动切换；暂不加入Motion Matching、布料和角色精修。
4. 用UAF调试信息或执行追踪确认图执行、输入变化及输出写入目标网格。对照关闭UAF输出或切换测试动作后的结果，排除实际仍由原AnimBP独立驱动。
5. 保存重开后重复验证。

通过条件：指定角色的最终姿态确实经过UAF，输入改变可解释地改变姿态。仅加载模板、播放一段普通动画或启用插件均不能算通过。

## M3 将UAF与Mover接到同一诊断角色

1. 创建项目内的 `BP_LabCharacter` 和 `L_ARPGMotionLab`；场景只含平地、墙、坡道和台阶。
2. 输入转成Mover的移动意图，由Mover负责碰撞位移；UAF读取运动状态并生成姿态。禁止另用逐帧SetActorLocation重复移动角色。
3. 单机优先评估Standalone后端；核实本版本实际配置入口，记录后端和Tick策略。
4. 先使用原地动画完成行走、停止和转向，检查移动方向、网格前方、胶囊和镜头一致。
5. 记录输入、速度、模式、胶囊位置及动画状态；每次只增加一个功能。

通过条件：同一角色同时运行Mover与UAF，移动和姿态一致，无双重位移和明显帧序错位。失败时分别回到M1或M2定位，再核对两者之间的数据与更新顺序。Mover的输入、状态、位移模式与后端职责参见[官方概念说明](https://dev.epicgames.com/documentation/en-us/unreal-engine/mover-features-and-concepts-in-unreal-engine)。

## M4 根运动与取消

1. 选一段有明确根位移的诊断动画，记录时长、根轨迹和理论位移。没有合适素材时制作简单诊断片段，不等待最终战斗动作。
2. 关闭Warping，先测完整播放。分别记录动画产生的位移、Mover消费的位移、碰撞后的实际位移；相同数据不能消费两次。
3. 在动作约25%、50%、75%位置注入取消，检查旧动作位移停止，并可恢复移动。
4. 立即重新执行该动作，重复10次；检查残留输入、旧回调、位移叠加和状态未复位。
5. 在空地与贴墙条件分别执行，并比较30/60/120 FPS限制下的结果。无法达到某限制时记录实际帧时间，不把设置值当实测值。

建议诊断容差：无障碍完整播放的平移误差不大于max(1 cm,理论距离的1%)，朝向误差不大于1度；取消后的旧动作贡献从下一次模拟更新起为零。容差是程序验证起点，若需调整须记录测量原因，不改变玩法判定标准。碰撞导致的实际位移减少应单独解释。

通过条件：位移来源唯一，取消和再入可重复，墙体阻挡正常，跨帧率差异在约定容差内。该阶段不承诺最终战斗手感。

## M5 有限范围的Motion Warping

1. M4通过后，增加单个固定目标和一个Warping窗口。
2. 分别测原距离、允许范围内的近距离与远距离、小角度转向。
3. 增加超出允许范围、目标消失、目标被墙阻挡，以及执行中取消的用例。
4. 记录原始根轨迹、目标、修正轨迹及碰撞后的实际轨迹。
5. 若官方Montage路径通过而纯UAF动作路径未通过，分别记录结果；不能把前者当成后者完成。

通过条件：允许范围内接触位置正确；越界时拒绝或采用明确备用行为；无穿墙、瞬移和取消残留。窗口和目标的公开用法见[Epic Motion Warping](https://dev.epicgames.com/documentation/en-us/unreal-engine/motion-warping-in-unreal-engine)。

## M6 固化技术样板

1. 以同一输入顺序执行：移动、开始诊断动作、取消、再次行动、贴墙动作、重置。
2. 保存重开后复验；至少制作一次Windows开发包并冷启动，确认编辑器外所需内容完整。
3. 记录固定分辨率与画质下的CPU/GPU帧时间、慢帧、显存和实际吞吐；此时只建立技术基线，不承诺最终主角性能。
4. 冻结资产、工程配置、运行包和结果，给出失败项及下一步。
5. 再做一次诊断动画或配置的小改动并重建，证明流程可重复。

通过条件：M0至M4成立，M5有明确结论或独立未完成项；打包与重建成功。若M5未通过，只可称“基础移动与动作样板通过”，不能称全组合通过。

## 角色资产到位后的接入

| 阶段 | 执行步骤 | 退出条件 |
|---|---|---|
| A0 接收 | 按交接清单核对文件、版本、依赖、可编辑源和运行参照；原包冻结，转换在副本进行 | 文件可打开、版本可对应、缺项明确 |
| A1 无模拟装配 | 灰材质检查身体、衣甲、毛领和披风；播放约定压力动作 | 已知露缝与硬甲形变问题有结论；错误回到源资产 |
| A2 接入技术样板 | 替换诊断网格，适配骨架与动作，复测M3至M5；还原同事镜头和控制参数 | 身份与美术标准保持，技术行为无退化 |
| A3 次级运动与交付 | 开启披风，做开关对照、多光照和实际镜头检查；重新测量、打包 | 程序技术项与策划视觉项分别通过 |

不得为接入方便自行更换主角形象、删去披风、降低材质标准或改变玩法。诊断动作不自动变成新玩法。素材未收到时，A0至A3为WAITING INPUT，不阻塞程序侧M0至M6。

## 每次运行留下什么

每次记录run-id、工程与资产版本、引擎、后端/Tick策略、测试条件、预期、实际、结论和证据位置。技术项用PASS、FAIL、NOT RUN；视觉项另列策划验收结果。

局部探索只跑受影响检查；形成阶段候选后才做完整回归和打包。所有示例、模块与模板的存在，只提供实现入口。[UAF官方索引](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/PluginIndex/UAF)、[Mover UAF桥接插件](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/PluginIndex/MoverAnimNext)。
