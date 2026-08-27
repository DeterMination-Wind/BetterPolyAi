# BetterPolyAi 开发指南（AGENTS.md）

本文件面向在本仓库工作的 AI 代理与人类协作者。面向人类的分类文档（架构 / 开发 / 发布 / 测试 / 术语表）在
[`docs/`](docs/README.md)。当本文件与上层工作区（BetterPolyAi 所在目录的上一级）的 AGENTS.md 冲突时，
以更贴近 Mindustry 模组任务的上层规范为准；本文件负责 BetterPolyAi 特有约束。

## 项目定位

- BetterPolyAi（模组 id `betterpolyai`，Neon 聚合侧 id `bpa`）是从 MindustryX BuilderAI 抽出的
  **纯客户端建造辅助模组**：驱动玩家自己操控的可建造单位补建玩家自己的建造规划，不接管其他玩家规划，
  不新增方块/单位，服务器无需安装。
- 本仓库是 BetterPolyAi 的**真源仓库**；Neon 聚合仓库中的同名代码是同步产物，改逻辑必须回到本仓库。

## 文件结构

```text
src/main/java/betterpolyai/
|-- BetterPolyAiMod.java          入口：init、设置分类注册、bekBundled 契约
|-- GithubUpdateCheck.java        GitHub 更新检查（仅独立形态）
`-- features/
    |-- PolyAiFeature.java            开关、设置、运行时状态机（normal/pausedByManualMove/yieldToX）
    |-- PlayerPlanBuilderAI.java      建造 AI：规划校验、自动移动、手动移动暂停
    `-- MindustryXBuilderAiProbe.java 反射探测 X 端 BuilderAI（编译期零 MindustryX 依赖）
src/main/resources/               mod.json 与 bundle*.properties（en/zh_CN/zh_TW）
.github/workflows/release.yml     推送 main 自动语义化发版
```

## 构建命令

```powershell
gradle clean deploy     # 全量：zip + 桌面 jar + 安卓 jar，自动复制到 ..\构建\betterPolyAi\
gradle compileJava      # 只编译检查
gradle jarAndroid       # 单独出安卓包（触发 D8）
```

- 无 Gradle wrapper，用系统 Gradle；JDK 17 跑构建，字节码目标 Java 8（源码禁用 Java 8 以上语法）。
- 依赖仅 `compileOnly` MindustryJitpack core（默认 v154.2）；不运行 gradle 也能做纯文档类改动。

## 代码约束

- 用户可见文案一律走 bundle，三份语言文件同步更新；设置 key `bpa-` 前缀。
- 编译期不得引用 `mindustryX.*` 类；对 MindustryX 的能力只走反射且失败静默回退。
- `init()` 与事件注册保持幂等守卫；聚合环境下初始化路径可能被重复触发。
- 只驱动玩家自己的单位；不得接管其他玩家或 AI 队伍的规划，玩家手动控制永远优先。

## 与 Neon 聚合的关系（bekBundled）

- 主类必须保留 `public static boolean bekBundled` 与 `public void bekBuildSettings(SettingsMenuDialog.SettingsTable)`。
- 所有 `ui.settings.addCategory(...)` 必须包在 `if(!bekBundled)` 内；`GithubUpdateCheck` 的默认值写入与检查同样受该守卫。
- `bekBuildSettings` 内更新检查两个 pref 也必须在 `!bekBundled` 时才添加（聚合侧更新由 Neon 收口）。
- Neon 通过 `tools/update_submods.py` 从本仓库同步；本仓库改动合入 main 后回 Neon 执行同步，不要在 Neon 里手改本模组代码。

## 验证清单

1. `gradle compileJava` 通过。
2. 原版客户端：开关/快捷键、建造辅助、手动移动暂停/恢复、toast 文案正常（见 [testing.md](docs/testing.md) 手测清单）。
3. MindustryX 客户端：选中 BuilderAI 时让出、取消后恢复。
4. 安卓包含 `classes.dex` 可加载。
5. 更新检查仅在独立形态运行，聚合形态无独立设置分类。

## 提交规范

- 提交信息前缀直接驱动 CI 发版：`feat:` 升次版本、`fix|perf|refactor|docs|...` 升修订号、`!:`/breaking change 升主版本；不想触发发版就用 `docs:` / `chore:`（见 [release.md](docs/release.md)）。
- 版本号与 CHANGELOG 由 CI 统一改写，不要手动提交 `chore(release): ...`。
- 只 add 本次任务相关文件，禁止 `git add -A` / `git add .`；提交前 `git diff --cached --stat` 核对。

## 文档索引

- [docs/README.md](docs/README.md)：文档导航
- [docs/architecture.md](docs/architecture.md)：架构总览
- [docs/development.md](docs/development.md)：开发指南
- [docs/release.md](docs/release.md)：版本与发布
- [docs/testing.md](docs/testing.md)：测试指南
- [docs/glossary.md](docs/glossary.md)：术语表
