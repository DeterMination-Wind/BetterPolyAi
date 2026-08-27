# 架构总览

改 `src/` 下的代码前先读本文。BetterPolyAi 是从 MindustryX BuilderAI 中抽出的纯客户端建造辅助模组：驱动玩家自己操控的可建造单位去补建玩家自己的建造规划（BuildPlan），同时保留玩家对单位的控制权。

## 入口与生命周期

单一入口：`mod.json` 的 `main` 指向 `betterpolyai.BetterPolyAiMod`（无 `mainX`；`hidden: true`，不在模组列表显示）。

1. **init()**：调用 `PolyAiFeature.init()`（幂等，`inited` 守卫）。
2. **ClientLoadEvent**：注册快捷键 `bpa-toggle`（默认 `P`，分类 `betterpolyai`）并刷新设置；独立形态下注册设置分类 `@settings.betterpolyai`（Icon.units）并执行 `GithubUpdateCheck.checkOnce()`。
3. **Trigger.update**：每帧进入 `PolyAiFeature.update()` 主循环。

## 核心类与数据流

| 类 | 职责 |
| --- | --- |
| `PolyAiFeature` | 总开关、设置读写（`bpa-enabled`、`bpa-mutex-with-x-polyai`，每 0.25s 刷新）、快捷键切换、运行时状态机 `RuntimeState`（normal / pausedByManualMove / yieldToX）、每帧守卫（对局中、世界未生成、玩家存活、单位可建造） |
| `features.PlayerPlanBuilderAI` | 继承 `AIController` 的建造 AI：校验当前 BuildPlan（`validPlace` / `validBreak` / ConstructBuild 匹配），无效规划直接移除；超出建造范围时 `moveTo` 靠近（上限取 `buildRange - hitSize*2` 与 `buildRadius=1500` 的较小值）；检测手动移动并暂停/恢复自主移动（恢复需静止约 12s）；地面单位 boost 判定；`fallback` 回退 PrebuildAI / FlyingAI / GroundAI |
| `features.MindustryXBuilderAiProbe` | 反射探测 `mindustryX.features.ui.OverlayUI` / `AuxiliaryTools#selectAI`（编译期不依赖 MindustryX）；X 端选中 BuilderAI 时返回 true |
| `betterpolyai.GithubUpdateCheck` | 仅独立形态运行：每 6 小时查 GitHub Releases API，失败回退 raw `mod.json`；弹窗或 toast 提示，支持忽略某版本 |

数据流：`Trigger.update → PolyAiFeature.update（守卫 + 让出判定）→ PlayerPlanBuilderAI.updateUnit → updateMovement（校验规划 → 移动/建造）`。游戏暂停时不跑 `updateUnit`（保留队列与暂停状态）；让出 X 端时完全停驱动。

## 设置系统

- `bpa-enabled`（默认关）：建造辅助总开关；`P` 键或设置页切换，切换有 toast 提示。
- `bpa-mutex-with-x-polyai`（默认开）：X 端 BuilderAI 选中时本帧让出单位控制。
- 更新检查两项（`bpa-updatecheck` / `bpa-updatecheck-dialog`）只在独立形态加入设置页。
- 快捷键在 `设置 → 控制` 的 `betterpolyai` 分组；文本输入框聚焦时按键不生效。

## Neon 聚合形态

主类提供聚合契约成员 `public static boolean bekBundled` 与 `bekBuildSettings(SettingsTable)`。被 Neon 聚合时 `bekBundled=true`：

- 跳过 `ui.settings.addCategory(...)`，改由 Neon 总设置页经 `bekBuildSettings` 挂入两个核心开关；
- 跳过 `GithubUpdateCheck`（默认值与检查都不执行），更新由聚合侧收口。

独立运行（`bekBundled=false`）时行为完全自管：自建设置分类 + 自查更新。

## 目录速查

```text
src/main/java/betterpolyai/
|-- BetterPolyAiMod.java          入口：init、设置分类注册、bekBundled 守卫
|-- GithubUpdateCheck.java        GitHub 更新检查（仅独立形态）
`-- features/
    |-- PolyAiFeature.java            开关与运行时状态机
    |-- PlayerPlanBuilderAI.java      建造 AI：规划校验、自动移动、手动暂停
    `-- MindustryXBuilderAiProbe.java 反射探测 X 端 BuilderAI
src/main/resources/
|-- mod.json                      模组描述
`-- bundles/bundle*.properties    en / zh_CN / zh_TW 文案
```

## 设计约束（务必保持）

- 纯客户端，不要求服务器安装；只驱动玩家自己的单位，不接管其他玩家或 AI 队伍的规划。
- 玩家控制权优先：快捷键可随时关断；手动移动立即暂停自主移动。
- 编译期不引用 `mindustryX.*` 类，对 MindustryX 的探测只走反射且失败静默回退。
- 用户可见文案走 bundle 三语文件；初始化路径全部幂等（`inited` / `settingsAdded` / `checked` 等守卫）。
