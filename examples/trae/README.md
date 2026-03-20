# 在 Trae 项目中使用 dev-agent-foundry

`dev-agent-foundry` 在 Trae 中的建议用法，是保留一层工具无关的共享能力文档，再通过 Trae 当前版本提供的项目级 instructions / rules 机制完成接入。

之所以这样设计，是因为不同工具的规则文件格式和产品约定会持续演进，但通用的 skill 协议、工作流约束和审查方式本身不应该频繁重写。

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
cp -r /path/to/dev-agent-foundry/skills/* /your-project/.agents/skills/
```

### 2. 准备一份工具无关的入口说明

建议保留一份项目级 `AGENTS.md`，集中描述通用能力：

```markdown
# Project Agent Guide

Reusable protocols live under `.agents/skills/`.

- `git-workflow`: delivery workflow
- `code-review-expert`: independent review
- `self-review`: author-side local validation
- `design-review`: design and architecture review
- `doc-gardening`: documentation consistency checks
```

### 3. 在 Trae 的项目规则或指令入口中挂接

根据 Trae 当前版本提供的项目级配置方式，将以下信息注册进去：

- 共享能力位于 `.agents/skills/`
- 遇到交付、评审、自审、文档修复等任务时应优先读取对应 skill
- 项目私有规则优先于通用默认值

## 接入建议

- 把 `AGENTS.md` 作为工具无关入口层
- 把 `Trae` 专属配置视为适配层，而不是能力定义层
- 当 Trae 的项目规则机制变化时，只调整适配层，不重写通用 skill
