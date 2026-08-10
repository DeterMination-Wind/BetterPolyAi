# Changelog

## v0.1.2
- ✨ 新增与 MindustryX BuilderAI 的互斥：检测到 X 端选中 BuilderAI 时自动让出单位控制（设置项「与X端PolyAi互斥」，默认开启）。
- ⏸ 新增手动移动时暂停/恢复自主移动的状态与提示。
- ⚙️ 移除「建造目标间距（格）」设置；设置整合进游戏设置菜单的 Better PolyAI 分类。
- 🛠 构建依赖改为解析 Arc 已发布 tag 产物（v155.4），修复 CI 解析 commit-hash 坐标失败的问题。
- 🏷 仓库元数据由备份仓库迁回 DeterMination-Wind/BetterPolyAi。

## v0.1.1
- 🐛 修复更新检查时间戳记录时机：仅在检查成功返回后记录，避免失败请求导致后续检查被跳过。
- 🏷 发布元数据切换到备份仓库 Wind-DeterMination-backup，版本号升至 v0.1.1。
- 🔧 将 Arc commit-hash 坐标重定向到已发布 tag（`476059`）。
- 👷 CI 重触发发布流程（`808ad1`）。

## v0.1.0
- ✨ 首次发布 BetterPolyAi：从 MindustryX 抽离 Poly 建造辅助功能。
- 🎯 限制行为为仅响应玩家自己的建造规划，不处理其他玩家规划。
- ⌨️ 新增键位绑定，按一下即可开关建造辅助。
- 🤖 发布工作流改为按 commit 关键字自动语义化升级并发布 Release。
