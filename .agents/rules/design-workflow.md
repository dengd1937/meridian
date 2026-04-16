# Design Workflow (Router)

> 完整流程在 `design-workflow` skill 中，按需加载。

## Activation

| Activate | Skip |
|---|---|
| 新 UI 功能 / 页面 / 组件 / 视觉改动 | 纯后端 / 配置 / 文档修改 |
| 新 token / 新交互模型 | 无视觉变化的重构 |
| 涉及 `docs/designs/`、`.pen`、Pencil MCP | 不涉及 UI |

## L1 vs L2 路由

- **L1（轻量）**：无新 token、无新组件、无新页面、无新交互 → 加载 `design-workflow` skill，执行 L1 章节
- **L2（标准）**：其他所有 UI 工作 → 加载 `design-workflow` skill，执行 V2-1 到 V2-5

## Handoff to Development

L2 Gate 3 通过后，产物进入 development-workflow：

- `intent.md` + `design.pen` + `screenshots/` → Step 1 Research
- `components/*.md` + `review-verdict.md` → Step 2 Plan
- `tokens/` → Step 3 TDD 验证

## Detail

完整流程（L2 五阶段、三个 Gate、目录布局、版本控制、shadcn 优先级）见 `design-workflow` skill。
