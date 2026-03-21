# Codex TDD Guide Adapter

这是 `tdd-guide` 在 Codex 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/tdd-guide/SKILL.md`

## 适配目标

在 Codex 中，`tdd-guide` 应被当作实现阶段的约束角色使用：

- 先写失败测试，再写生产代码
- 强制执行 `RED -> GREEN -> REFACTOR`
- 用自动化测试驱动 bug 修复、功能实现和回归保护

## 触发条件

以下任务默认进入 `tdd-guide`：

- 新功能实现
- bug 修复
- 行为调整
- 需要补回归保护的重构

以下任务通常不需要单独触发 `tdd-guide`：

- 纯文档修改
- 无可观察行为变化的配置变更
- 探索性原型或一次性脚本
- 已明确授权跳过 TDD 的场景

## 必须遵循

在 Codex 中使用 `tdd-guide` 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/tdd-guide/SKILL.md`
3. 相关 `rules/` 文档

如果任务范围或阶段拆分仍不清晰，应先回到 `planner`。

## 输出要求

输出至少应说明：

- 当前正在实现的行为
- 对应失败测试
- 失败原因是否符合预期
- 当前最小实现范围
- 已通过的测试
- 尚未覆盖的边界或下一步测试点

## 交接要求

`tdd-guide` 完成后：

- Python 项目里的框架细节交给 `python-testing`
- 代码改动完成后交给 `code-review-expert`
- 不直接承担 commit / PR / CI 流程
