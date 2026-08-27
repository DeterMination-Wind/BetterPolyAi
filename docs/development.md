# 开发指南

环境、日常构建命令与调试建议。仓库级约束见根目录 [AGENTS.md](../AGENTS.md)，两者冲突时以 AGENTS.md 为准。

## 环境

- 运行构建用 JDK 17（CI 使用 Temurin 17）；字节码目标是 Java 8（`options.release.set(8)`），**源码只能用 Java 8 语法**。
- 仓库**没有 Gradle wrapper**，直接用系统 Gradle（README 与 CI 均为 `gradle deploy`）。
- 依赖：`compileOnly com.github.Anuken.MindustryJitpack:core`（默认 `v154.2`，可用 `-PmindustryVersion=` 覆盖）；Arc 坐标自动重定向到已发布 tag `v155.4`（`arcVersion` 属性）。
- 安卓 dex 需要 D8：按 `D8_PATH` 环境变量 → `ANDROID_SDK_ROOT` / `ANDROID_HOME` 的 `build-tools` → 工作区根 `commandlinetools-win-*` 目录顺序探测，找不到时 `dexAndroid` 直接失败。

## 常用命令

```powershell
# 完整构建：三类产物并拷贝到 ..\构建\betterPolyAi\
gradle clean deploy

# 只编译检查
gradle compileJava

# 单独出某类产物（均会自动拷贝到构建目录）
gradle jar          # betterPolyAi.zip（模组主产物）
gradle jarDesktop   # betterPolyAi.jar（桌面 jar）
gradle jarAndroid   # betterPolyAi-android.jar（含 classes.dex，触发 d8InputJar + dexAndroid）
```

产物复制约定：`jar` / `jarDesktop` / `jarAndroid` 分别 `finalizedBy` 对应 Copy 任务，把产物按 `betterPolyAi-<version>*` 重命名到 `../构建/betterPolyAi/`，细节见 [release.md](release.md)。

## 代码风格要点

- Java 8 语法，文件编码 UTF-8（`options.encoding`）；标识符常规 Java 命名。
- 功能逻辑集中在 `features.PolyAiFeature` 的静态持有者中；工具类私有构造（如 `GithubUpdateCheck`）。
- 设置 key 用 `bpa-` 前缀，bundle key 用 `bpa.` / `settings.betterpolyai` 前缀；新增文案必须同步 `bundle.properties`、`bundle_zh_CN.properties`、`bundle_zh_TW.properties` 三份。
- 初始化与事件注册保持幂等守卫（`inited` / `settingsAdded` / `keybindsRegistered` / `checked`），`init()` 可能被聚合环境多次触发。
- 不新增编译期 `mindustryX.*` 依赖，外部探测一律反射 + 静默回退（见 `MindustryXBuilderAiProbe`）。

## 调试建议

- 状态迁移都有 toast（`bpa.toast.*`）：开关、手动移动暂停/恢复、让出 X 端，可据此确认状态机走向。
- 设置每 0.25s 从 `Core.settings` 刷新，改设置无需重载。
- 验证安卓可用性以 `jarAndroid` 产物内含 `classes.dex` 为准；桌面 jar 不含 dex。
- 先在原版客户端验证完整功能，再装 MindustryX 验证互斥让出路径。
