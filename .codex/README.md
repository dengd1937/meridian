# Codex Adapter

`.codex/` 是 Codex 的适配层。

当前策略：

- 共享 skill 真源仍然维护在 `.agents/skills/`
- 根级 `AGENTS.md` 继续作为 Codex 的项目总入口
- `planner`、`tdd-guide`、`code-review-expert` 先通过蓝图文档做适配，不急于声明一个未经公开文档确认的项目级 subagent 文件格式

如果后续 Codex 官方公开更明确的项目级 agent/subagent 仓库格式，再把这里升级成自动装载的薄 wrapper。
