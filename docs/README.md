# BetterPolyAi 文档

BetterPolyAi 的分类文档。文档以中文为主，功能名保留英文本名，便于与代码和设置界面对照。

## 从哪里开始

| 你是 | 从这里开始 |
| --- | --- |
| 玩家 | [README](../README.md)（安装与一句话说明） |
| 想了解模组怎么实现的人 | [架构总览](architecture.md) |
| 想改代码 / 调构建 | [开发指南](development.md) |
| 准备发版 | [版本与发布](release.md) |
| 改动后要验证 | [测试指南](testing.md) |
| 遇到不认识的词 | [术语表](glossary.md) |

## 文档地图

```text
docs/
|-- README.md          本页：文档导航
|-- architecture.md    架构总览：入口、生命周期、AI 状态机、聚合形态
|-- development.md     开发指南：环境、构建命令、调试
|-- release.md         版本与发布：语义化版本、产物链、CI
|-- testing.md         测试指南：手测清单（本仓库无自动化测试）
`-- glossary.md        术语表
```

## 相关文件

- [CHANGELOG.md](../CHANGELOG.md)：版本历史（CI 发版时自动追加条目）。
- [AGENTS.md](../AGENTS.md)：仓库维护约束（bekBundled 契约、提交规范、验证清单）。
- [README.md](../README.md) / [README_EN.md](../README_EN.md)：项目主页（中/英）。
- `src/main/resources/mod.json`：模组描述（入口、版本、`minGameVersion`）。

## 维护约定

- 用户可见文案一律走 `src/main/resources/bundles/bundle*.properties`（en / zh_CN / zh_TW 三份），新增 key 必须三语同步，不硬编码。
- 文档只描述当前源码的真实行为；改行为先改代码与本文档，再提交。
- 提交信息决定 CI 是否发版（见 [release.md](release.md)），纯文档改动用 `docs:` 前缀。
