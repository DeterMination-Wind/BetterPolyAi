# 术语表

按主题归类的 BetterPolyAi 术语。每条说明它在本模组语境下的准确含义。

## 功能概念

### 建造辅助（build assist）
驱动玩家当前操控、且 `canBuild()` 的单位去执行玩家自己的建造/拆除规划。名字源于 Poly，但实际作用于任何可建造单位。

### BuildPlan
Mindustry 的建造规划条目（方块、位置、旋转、是否拆除）。模组只消费玩家规划队列，不自行生成规划；失效条目会被 `PlayerPlanBuilderAI` 移除。

### buildRadius
模组允许自动移动趋近的最大距离常量（1500 格）。实际移动目标范围取 `单位 buildRange - hitSize*2` 与它的较小值。

### 手动移动暂停（pausedByManualMove）
检测到玩家手动操控（位移或速度超阈值、背离建造目标）时暂停 AI 自主移动的状态；单位静止约 12 秒后自动恢复，期间有 toast 提示。

### 让出 / 互斥（yieldToX）
`bpa-mutex-with-x-polyai` 开启时，若 MindustryX 的 AuxiliaryTools 当前选中 BuilderAI，模组本帧起停止驱动单位，把控制权让给 X 端 AI；取消选中后自动恢复。

### MindustryXBuilderAiProbe
对 `mindustryX.features.ui.OverlayUI` / `AuxiliaryTools#selectAI` 的反射探测器。编译期不依赖 MindustryX，任何解析失败都静默视为"未选中"。

## 项目形态

### bekBundled
主类上的静态布尔标记，表示"当前运行在 Neon 聚合环境中"。为 `true` 时跳过自建设置分类与更新检查，设置改由 `bekBuildSettings` 挂入 Neon 总设置页。

### 独立形态 / 聚合形态
`bekBundled=false` 时模组自管设置分类与 GitHub 更新检查；`true` 时两者都交由 Neon 收口。两种形态共用同一套 AI 逻辑。

### 纯客户端（client-side）
全部功能运行在玩家自己的客户端，不要求服务器安装，其他玩家不感知。

## 构建与发布

### deploy
聚合构建任务 = `jar` + `jarDesktop` + `jarAndroid`，并把三类产物按版本号重命名复制到 `../构建/betterPolyAi/`。`.zip` 是游戏内安装推荐产物。

### D8 / classes.dex
把 Java 字节码转成安卓 `classes.dex` 的工具链。安卓端加载 Java 模组必须含 dex；桌面 jar 不含它，不能当作安卓产物分发。

### bundle
Mindustry 的 i18n 文件。本模组维护 `bundle.properties` / `bundle_zh_CN.properties` / `bundle_zh_TW.properties` 三份，key 以 `bpa.` 前缀命名。

### 语义化发布（semver release）
CI 依据提交信息前缀（`feat` / `fix` / `!:` 等）自动计算 `vX.Y.Z`、改写版本文件、更新 CHANGELOG、打 tag 并创建 Release。详见[版本与发布](release.md)。
