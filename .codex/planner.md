# Codex Planner Adapter

这是 `planner` 在 Codex 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/planner/SKILL.md`

## 适配目标

在 Codex 中，`planner` 应被当作一个前置角色使用：

- 在实现前先输出结构化实施计划
- 不直接进入编码
- 计划完成后再交给实现流程继续推进

## 触发条件

以下任务默认先进入 planner：

- 新功能开发
- 中大型 bug 修复
- 多文件改动
- 重构
- 架构边界调整

以下任务通常不需要单独触发 planner：

- 单文件小修
- 纯说明性文档变更
- 已有完整执行计划的简单落地

## 必须遵循

在 Codex 中使用 planner 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/planner/SKILL.md`
3. 相关 `rules/` 文档

## 输出要求

输出必须是 handoff-ready 的实施计划，至少包括：

- Overview
- Scope / Out of scope
- Existing Patterns / Reuse
- Affected Areas
- Implementation Phases
- Testing Strategy
- Risks & Mitigations
- Open Questions
- Success Criteria

## 交接要求

planner 完成后：

- 交给实现相关 skill 或 agent
- 不自行进入 commit / PR / CI 流程
- 如果计划仍有关键未知项，必须显式写在 `Open Questions`
