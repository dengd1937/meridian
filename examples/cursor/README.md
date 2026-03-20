# 在 Cursor 项目中使用 dev-agent-foundry

在 Cursor 中，推荐把 `dev-agent-foundry` 分成两层接入：

- 通用能力层：项目内保存共享 skill 文档
- Cursor 适配层：使用 `.cursor/rules/` 把这些能力挂接到 Agent

这能减少规则和通用能力定义的耦合，后续迁移到其他工具也更容易。

## 推荐结构

```text
your-project/
├── AGENTS.md
├── .agents/
│   └── skills/
│       ├── git-workflow/
│       ├── code-review-expert/
│       └── ...
└── .cursor/
    └── rules/
        └── dev-agent-foundry.mdc
```

## 快速接入

### 1. 复制需要的 skill

```bash
cp -r /path/to/dev-agent-foundry/skills/* /your-project/.agents/skills/
```

### 2. 新增一条 Cursor Project Rule

在 `.cursor/rules/dev-agent-foundry.mdc` 中写一条项目规则，把通用 skill 作为可复用协议暴露给 Cursor Agent。

```md
---
description: Use shared workflow and review skills from .agents/skills when relevant.
alwaysApply: false
---

Shared reusable protocols live under `.agents/skills/`.

When a task matches one of the following capabilities, read the corresponding skill before acting:

- `git-workflow`
- `code-review-expert`
- `self-review`
- `design-review`
- `doc-gardening`

Prefer project-specific rules when they conflict with generic defaults.
```

### 3. 可选保留 AGENTS.md 作为工具无关入口

如果团队同时使用 Codex、Cursor、Claude Code 等多个工具，建议保留一份 `AGENTS.md`，用来解释共享能力所在位置和基本使用原则。这样即使后续调整 Cursor 的项目规则写法，也不需要重写通用说明层。

## 接入建议

- 通用能力保留在 `.agents/skills/`
- Cursor 专属行为放在 `.cursor/rules/`
- 不要把大量工具专属元数据直接写进通用 skill 文档
- 若已有 `.cursorrules`，建议逐步迁移到 `.cursor/rules/`
