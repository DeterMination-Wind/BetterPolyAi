# 版本与发布

BetterPolyAi 的发版由 CI 全自动完成：**推送到 main 的提交信息决定是否发版与版本号**，本地只负责构建验证。

## 版本号体系

- 语义化版本 `vX.Y.Z`（当前 `v0.1.1`），写入 `build.gradle` 与 `src/main/resources/mod.json`，两者必须一致。
- CI（`.github/workflows/release.yml`，推送 main 触发）按上个 `v*` tag 以来的提交自动升版：
  - `feat` → 次版本；`fix|perf|refactor|docs|style|test|build|ci|chore|revert` → 修订号；`!:` 或 body 含 `breaking change` → 主版本；
  - 无匹配提交则跳过发版。
- 发版时 CI 自动改写两处版本号、把提交清单追加进 `CHANGELOG.md`、提交 `chore(release): vX.Y.Z [skip release]`、打 tag 并创建 Release。手写 CHANGELOG 条目可以早于版本号落库。
- 手动发版（不推荐）需自行同步 `build.gradle` 与 `mod.json` 两处版本。

## 构建产物链

`deploy` = `jar` + `jarDesktop` + `jarAndroid`：

| 任务 | 产物 | 复制到 `../构建/betterPolyAi/` |
| --- | --- | --- |
| `jar` | `build/libs/betterPolyAi.zip` | `betterPolyAi-<version>.zip`（**游戏内安装推荐产物**） |
| `jarDesktop` | `build/libs/betterPolyAi.jar` | `betterPolyAi-<version>.jar`（桌面 jar） |
| `jarAndroid` | `build/libs/betterPolyAi-android.jar` | `betterPolyAi-<version>-android.jar` |

`jarAndroid` 依赖 `d8InputJar` → `dexAndroid`（D8 `--min-api 14 --release`，`--lib android.jar` + 编译类路径）产出 `classes.dex`。

## Release 资产安全规则

- Mindustry 游戏内安装器会取 Release API 返回的**第一个 `.jar`**，且不按操作系统挑资产。玩家安装请优先使用 `.zip` 资产。
- 一个 Release 必须有且只有一个最终可安装 jar（含 `mod.json` + 桌面主类 + `classes.dex`）；`betterPolyAi-<version>.jar`（桌面）与 `-android.jar` 是平台中间产物，按工作区规则不得与最终安装包并列发布。
- 发布后用 API 复查资产并移除多余 `.jar`：
  ```bash
  gh release view <tag> --json assets
  ```
