# Cursor Adapter

`.cursor/` 是 Cursor 的适配层。

共享 skill 真源不放在这里，而是通过 `skills -> ../.agents/skills` 软链接接入。

当前已补的适配入口：

- `.cursor/rules/planner.mdc`
- `.cursor/rules/tdd-guide.mdc`
- `.cursor/rules/code-review-expert.mdc`

这里优先使用 Cursor 官方 Project Rules 体系，而不是自定义仓库内 subagent 文件格式。这些 `.mdc` 文件应作为 `Agent Requested` rule 使用。
