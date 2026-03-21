# Codex Code Review Expert Adapter

这是 `code-review-expert` 在 Codex 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/code-review-expert/SKILL.md`

## 适配目标

在 Codex 中，`code-review-expert` 应被当作一个独立 reviewer 入口使用：

- 在 PR 阶段执行 findings-first 的独立 review
- 不复用 Author 的上下文
- 输出可直接回写到 PR 的结构化结论

## 触发条件

以下情况默认触发 `code-review-expert`：

- 创建或更新 PR 后
- merge 前需要一次独立质量门禁
- 需要对明确 diff 范围做独立审查

## 必须遵循

在 Codex 中使用 `code-review-expert` 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/code-review-expert/SKILL.md`
3. 相关 `rules/` 文档

并满足以下要求：

- 运行在与 Author 隔离的 review 上下文中
- 优先读取审查范围、diff、关键文件和验证证据
- 默认只输出 findings，不直接改代码

## 输出要求

输出必须是 findings-first 的结构化 review，至少包括：

- Review mode
- Scope
- Overall assessment
- Evidence checked
- Findings
- Residual Risks

## 交接要求

`code-review-expert` 完成后：

- 结果应优先回写到 PR review 或 PR comment
- Author 负责读取 findings 并修复
- reviewer 不直接承担实现或交付职责
