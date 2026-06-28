# src/main/java/betterpolyai/features 层说明

这一层是项目最核心的功能代码区，真正决定 Poly 建造辅助怎么工作。

## 当前文件职责

- `PolyAiFeature.java`：管理设置键、按键绑定、设置刷新频率、开关状态、提示文字，以及每帧的运行态切换，包括 X 端互斥和手动接管暂停。
- `PlayerPlanBuilderAI.java`：继承 `AIController`，只围绕当前单位的建造计划工作；会校验待建方块或拆除请求是否合法，采用“在范围内不动、超范围才补到边界”的懒移动策略，并在必要时回退到 `PrebuildAI`、`FlyingAI`、`GroundAI`。
- `MindustryXBuilderAiProbe.java`：通过反射兼容探测 `MindustryX` 的 `OverlayUI/AuxiliaryTools` 状态，失败时静默降级。

## 实现细节

- `PolyAiFeature` 每 0.25 秒刷新一次设置，说明作者愿意接受轻量轮询，换取设置即时生效而不需要额外事件绑定。
- 默认开关键是关闭的，快捷键是 `P`。
- 新的移动规则不再暴露 gap 配置，而是直接以实际建造范围为边界，减少贴脸靠近导致的无效位移。
- 当玩家主动把单位移离 AI 预期位置时，会暂停自主移动；单位连续静止约 `0.2s` 后恢复，但建造执行本身不会被强制关闭。
- `PlayerPlanBuilderAI` 明确只消费 `unit.buildPlan()`，不会主动替别人生成计划，也不会扩散到其他玩家的建造队列。

## 和其他层级的关系

- 上游由 `BetterPolyAiMod` 初始化。
- 下游行为文案来自 `src/main/resources/bundles` 中的 key。
