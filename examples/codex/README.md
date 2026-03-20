# 在 Codex 项目中使用 dev-agent-foundry

`dev-agent-foundry` 在 Codex 中的推荐接入方式，是把通用能力保留在项目目录里，再通过 `AGENTS.md` 暴露给 Codex。

这种方式的好处是：

- 项目规则可版本化
- 技能定义不依赖单一工具格式
- 后续迁移到其他工具时可以复用同一批文档

## 推荐结构

```text
your-project/
├── AGENTS.md
└── .agents/
    └── skills/
        ├── git-workflow/
        ├── code-review-expert/
        └── ...
```

## 快速接入

### 1. 复制需要的 skill

```bash
# 复制全部 skill
cp -r /path/to/dev-agent-foundry/skills/* /your-project/.agents/skills/

# 或按需复制
cp -r /path/to/dev-agent-foundry/skills/git-workflow /your-project/.agents/skills/
cp -r /path/to/dev-agent-foundry/skills/code-review-expert /your-project/.agents/skills/
```

### 2. 在 AGENTS.md 中声明能力入口

在项目根目录创建或更新 `AGENTS.md`，明确告诉 Codex 这些能力在哪里、什么时候该用。

```markdown
# Project Agent Guide

## Shared Skills

Reusable protocols live under `.agents/skills/`.

- `git-workflow`: use for commit / push / PR / CI / merge delivery requests
- `code-review-expert`: use for independent review focused on findings
- `self-review`: use for author-side local validation before commit
- `design-review`: use for reviewing plans, architecture, or design docs
- `doc-gardening`: use for checking and repairing doc drift

## Project Notes

- Read project-specific architecture rules before editing.
- Prefer project-local standards over generic defaults when they conflict.
```

### 3. 在实际任务中引用这些能力

常见触发方式：

- 交付请求：使用 `git-workflow`
- 独立审查：使用 `code-review-expert`
- 设计评审：使用 `design-review`
- 提交前本地检查：使用 `self-review`

## 接入建议

- `AGENTS.md` 负责描述“有哪些能力、何时使用”
- 具体协议细节保留在 `.agents/skills/` 对应目录中
- 如果项目还有更强的本地规范，应在 `AGENTS.md` 中优先声明，再把 `dev-agent-foundry` 作为复用层
