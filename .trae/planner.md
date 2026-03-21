# Trae Planner Adapter

这是 `planner` 在 Trae 中的适配蓝图，不是共享真源。

共享真源位于：

- `../.agents/skills/planner/SKILL.md`

## 适配思路

Trae 当前更适合把 planner 映射成：

- 一条可请求的 Rule
- 一个可复用的 Agent Skill 入口

也就是说，Trae 侧不应该复制 planner 正文，而应通过 Rule/Skill 入口把请求导向 shared planner skill。

## 触发条件

以下任务应优先触发 planner：

- 新功能开发
- 中大型 bug 修复
- 跨文件重构
- API、数据模型、配置流调整
- 需要先拆阶段再实施的任务

## 必须遵循

在 Trae 中使用 planner 时，应先读取：

1. `AGENTS.md`
2. `.agents/skills/planner/SKILL.md`
3. 相关 `rules/` 文档

## 输出要求

planner 的输出不是讨论摘要，而是可执行计划。至少包含：

- 范围
- 复用点
- 影响面
- 分阶段步骤
- 风险
- 验证策略
- 成功标准

## 交接要求

planner 完成后：

- 把结果交给后续实现 agent / skill
- 不直接充当 reviewer
- 不直接承担交付流程
