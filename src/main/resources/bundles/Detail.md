# src/main/resources/bundles 层说明

这一层集中管理本模组的国际化文本。

## 当前文件

- `bundle.properties`：默认英文文本。
- `bundle_zh_CN.properties`：简体中文文本。

## 覆盖的文案范围

- 设置分类标题 `settings.betterpolyai`
- 快捷键名称 `keybind.bpa-toggle.name`
- 自动建造、X 端互斥、更新检查、弹窗提示等设置项说明
- 功能启停、X 端避让、手动接管暂停/恢复等短提示

## 实现方式

代码层通过 `Core.bundle.get(...)` 或 Mindustry 设置系统自动按 key 取文案，因此这里的 key 命名直接反映了代码接口设计。

## 层级关系

- 上游与 `PolyAiFeature`、`BetterPolyAiMod`、`GithubUpdateCheck` 的 UI 交互点强绑定。
- 下游进入所有模组发布包。
