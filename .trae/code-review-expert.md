# Trae Code Review Expert Adapter

这是 `code-review-expert` 在 Trae 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/code-review-expert/SKILL.md`

## 适配思路

Trae 当前更适合把 `code-review-expert` 映射成：

- 一条可请求的 Rule
- 一个独立 reviewer 的 Agent Skill 入口

也就是说，Trae 侧不应复制 review 正文，而应通过 Rule/Skill 入口把请求导向 shared review skill。

## 触发条件

以下任务应优先触发 `code-review-expert`：

- PR 阶段的独立 review
- merge 前的最终独立审查
- 需要对明确 diff 范围做隔离审查

## 必须遵循

在 Trae 中使用 `code-review-expert` 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/code-review-expert/SKILL.md`
3. 相关 `rules/` 文档

并满足以下要求：

- 运行在与 Author 隔离的 review 上下文中
- 优先读取 diff、关键文件和验证证据
- 默认只输出 findings，不直接改代码

## 输出要求

review 输出至少包含：

- 审查范围
- 审查结论
- Findings
- Residual Risks

## 交接要求

`code-review-expert` 完成后：

- 结果交回 PR review 或 PR comment
- Author 负责处理 findings
- reviewer 不直接进入修复或提交流程
