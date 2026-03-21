# Trae TDD Guide Adapter

这是 `tdd-guide` 在 Trae 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/tdd-guide/SKILL.md`

## 适配思路

Trae 当前更适合把 `tdd-guide` 映射成：

- 一条可请求的 Rule
- 一个可复用的 Agent Skill 入口

Trae 侧不应复制 `tdd-guide` 正文，而应通过 Rule/Skill 入口把实现阶段导向 shared TDD skill。

## 触发条件

以下任务应优先触发 `tdd-guide`：

- 新功能实现
- bug 修复
- 需要回归保护的行为调整
- 测试优先的重构

## 必须遵循

在 Trae 中使用 `tdd-guide` 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/tdd-guide/SKILL.md`
3. 相关 `rules/` 文档

如果任务还没有明确范围或实施阶段，应先回到 `planner`。

## 输出要求

`tdd-guide` 的输出不是“我已经修好了”，而是可验证的实现证据，至少包含：

- 当前行为
- 失败测试
- 最小实现范围
- 已通过测试
- 剩余边界或回归点

## 交接要求

`tdd-guide` 完成后：

- 继续交给语言层测试 skill 或实现流程
- 代码完成后交给 reviewer
- 不直接承担交付流程
