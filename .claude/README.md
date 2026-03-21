# Claude Adapter

`.claude/` 是 Claude Code 的适配层。

共享 skill 真源不放在这里，而是通过 `skills -> ../.agents/skills` 软链接接入。

当前已补的适配入口：

- `.claude/agents/planner.md`
- `.claude/agents/tdd-guide.md`
- `.claude/agents/code-review-expert.md`

这里使用 Claude Code 官方项目级 subagent 格式，把共享 skill 包装成薄 agent wrapper。
