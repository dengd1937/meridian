# dev-agent-foundry — Agent Instructions

这是 `dev-agent-foundry` 的总入口文档。这个仓库的目标，是沉淀一套可跨项目、跨工具复用的 dev-agent 资产，让 Claude Code、Codex、Cursor、Trae 等工具围绕同一批共享 `rules` 与 `skills` 协作。

## Core Principles

1. **Rules-First** — 先读规则，再执行任务
2. **Skill-as-Protocol** — skill 负责具体任务协议，rules 负责默认约束
3. **Test-Driven** — 默认采用 `RED -> GREEN -> REFACTOR`
4. **Security-First** — 默认校验输入、保护敏感信息、收紧权限边界
5. **Plan Before Execute** — 复杂改动先规划，再实现

## Available Skills

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `git-workflow` | Git / PR / CI 交付协议 | 提交、推送、创建 PR、跟进 CI、推进合并 |
| `code-review-expert` | 独立 reviewer 协议 | 需要 findings-first 的结构化代码审查 |
| `design-review` | 设计评审 | 方案、架构、边界、可维护性评估 |
| `doc-gardening` | 文档治理 | 检查并修复文档与代码、规则、目录的漂移 |
| `planner` | 实施规划 | 多步骤任务的范围澄清、影响面分析、阶段拆分与 handoff-ready 计划输出 |
| `tdd-guide` | 测试优先实现 | 用失败测试驱动实现，执行 `RED -> GREEN -> REFACTOR` |
| `python-patterns` | Python 开发模式 | 编写、重构、审查 Python 代码 |
| `django-security` | Django 安全实践 | Django 认证鉴权、部署、安全审查 |
| `python-testing` | Python 测试策略 | pytest、TDD、fixtures、mock、覆盖率 |

## Skill Orchestration

无需等待用户显式指定 skill；当任务类型已经明确时，主动使用最合适的 skill。

- 复杂方案、边界梳理、重构拆分 → `design-review`
- 新功能、多文件改动、复杂重构开始前 → `planner`
- 进入实现阶段并采用测试优先推进 → `tdd-guide`
- PR 阶段必须在隔离的 review 上下文中执行独立 review → `code-review-expert`
- 提交、推送、PR、CI、合并 → `git-workflow`
- 文档补全、目录治理、规则同步 → `doc-gardening`
- 编写或重构 Python 代码 → `python-patterns`
- Django 安全相关代码或配置 → `django-security`
- Python 测试设计与实现 → `python-testing`

多个相互独立的动作可以并行推进，但不要让不同 skill 处理同一段未收敛的变更。

## Rule Navigation

`rules/` 是默认应遵守的规则层，优先于 skill 内的任务协议。先读 `common`，再按技术栈叠加语言层规则。

### Common Rules

- [rules/common/development-workflow.md](rules/common/development-workflow.md)
- [rules/common/coding-style.md](rules/common/coding-style.md)
- [rules/common/patterns.md](rules/common/patterns.md)
- [rules/common/performance.md](rules/common/performance.md)
- [rules/common/security.md](rules/common/security.md)
- [rules/common/testing.md](rules/common/testing.md)
- [rules/common/git-workflow.md](rules/common/git-workflow.md)
- [rules/common/ci-workflow.md](rules/common/ci-workflow.md)

### Python Rules

- [rules/python/coding-style.md](rules/python/coding-style.md)
- [rules/python/patterns.md](rules/python/patterns.md)
- [rules/python/security.md](rules/python/security.md)
- [rules/python/testing.md](rules/python/testing.md)

默认读取顺序：

1. 从 [rules/common/development-workflow.md](rules/common/development-workflow.md) 进入开发主流程
2. 涉及编码约束时读取 [rules/common/coding-style.md](rules/common/coding-style.md)
3. 涉及模式、性能、安全、测试时读取对应 `common` 主题文档
4. 涉及 Python 项目时叠加 `rules/python/`
5. 进入提交、PR、review、merge 阶段后切到 [rules/common/git-workflow.md](rules/common/git-workflow.md)
6. 进入 CI 阶段后参考 [rules/common/ci-workflow.md](rules/common/ci-workflow.md)

## Security Guidelines

在任何交付动作前，至少确认：

- 不得硬编码 secret、token、密码、证书或密钥
- 所有外部输入都必须在系统边界做校验
- 数据访问默认使用参数化查询或等价防注入机制
- 错误信息和日志不得泄露敏感数据
- 发现高风险安全问题时，先停止交付，再修复问题

如需更细规则，读取 [rules/common/security.md](rules/common/security.md)。Django 项目再叠加 `django-security`。

## Coding Style

默认编码基线：

- 优先不可变更新，避免随意原地修改共享状态
- 保持文件高内聚、低耦合，避免单文件持续膨胀
- 错误必须显式处理，禁止静默吞错
- 输入校验前置在系统边界
- 命名直接表达意图，优先可读性而不是技巧性

通用规则见 [rules/common/coding-style.md](rules/common/coding-style.md)。Python 项目叠加 [rules/python/coding-style.md](rules/python/coding-style.md)。

## Testing Requirements

默认测试要求：

- 最低覆盖率目标为 `80%`
- 单元测试、集成测试、端到端测试应按项目实际情况覆盖关键路径
- 默认采用 `RED -> GREEN -> REFACTOR`
- 测试失败时优先检查隔离性、fixture、mock 和实现，不要直接修改断言掩盖问题

通用规则见 [rules/common/testing.md](rules/common/testing.md)。Python 项目叠加 [rules/python/testing.md](rules/python/testing.md)。

## Development Workflow

默认开发主线：

1. **Research & Reuse** — 先查已有实现、模板、文档和成熟方案
2. **Plan First** — 复杂改动先规划依赖、风险和阶段
3. **TDD Approach** — 优先使用 `tdd-guide`，先写失败测试，再最小实现，再重构
4. **Commit & Push** — 进入交付阶段后遵循 `git-workflow`

详细流程见 [rules/common/development-workflow.md](rules/common/development-workflow.md)。

## Git Workflow

交付阶段默认采用 Pull Request 工作流：

- 禁止直接推送到主分支
- `push` 不是完成态，`PR 已创建` 通常也不是完成态
- review、CI、反馈处理、merge 都属于交付流程的一部分
- 需要独立 reviewer 时，优先使用 `code-review-expert`

详细规则见 [rules/common/git-workflow.md](rules/common/git-workflow.md)。CI 检查细则见 [rules/common/ci-workflow.md](rules/common/ci-workflow.md)。

## Architecture Patterns

通用模式约束：

- 优先复用成熟实现，而不是为小问题新造抽象
- 抽象边界应服务业务和维护成本，而不是服务目录美观
- 公共协议、数据结构和边界约束应显式表达
- 当模式选择影响复杂度、可测试性或扩展性时，先做设计评审

详细规则见 [rules/common/patterns.md](rules/common/patterns.md)。Python 项目叠加 [rules/python/patterns.md](rules/python/patterns.md)。

## Performance

性能基线：

- 优先消除明显低效实现，再考虑复杂优化
- 仅在有证据时做性能优化，避免预优化
- 大改动或多文件重构时，保持上下文收敛，分阶段推进和验证
- 引入缓存、批处理、并发前，先明确一致性与失败语义

详细规则见 [rules/common/performance.md](rules/common/performance.md)。

## Tool Adapters

工具适配层只负责接入，不应成为共享资产真源。

- `.claude/`
- `.codex/`
- `.cursor/`
- `.trae/`

共享 skill 真源统一维护在 `.agents/skills/`。如需为某个工具建立映射，应优先指向共享 skill，而不是复制一份新内容。

- `.agents/skills/<name>/SKILL.md` 是唯一真源
- 工具适配层只做薄包装，不复制完整 skill 正文
- 适配文件至少应补齐 `when_to_use`、`must_follow`、`allowed_tools`、`output_contract`、`handoff_contract`
- 优先使用该工具公开且稳定的项目级格式
- 如果官方仓库级格式不够明确，则先落为 blueprint 文档，不发明伪标准
- Claude Code 当前优先使用项目级 agent wrapper，例如 `.claude/agents/<name>.md`
- Cursor 当前优先使用公开且稳定的 Project Rules 入口；如果后续仓库级 agent 格式更稳定，再迁移成更薄的 wrapper
- Codex 当前以 `AGENTS.md` + shared skills 为主，工具目录中的文档先作为 blueprint
- Trae 当前以 shared skills 为真源，工具目录中的文档先作为 blueprint，等待更稳定的仓库级约定

## Priority Order

当规则冲突时，遵循以下优先级：

1. 项目自己的明确规则
2. 项目级 `AGENTS.md`
3. 本仓库语言层规则，例如 `rules/python/`
4. 本仓库 `rules/common/`
5. `.agents/skills/` 中的任务协议
6. 工具自身默认行为

当项目私有规则与通用规则冲突时，优先项目私有规则。

## Project Structure

```text
dev-agent-foundry/
├── AGENTS.md
├── .agents/
│   └── skills/
├── rules/
│   ├── common/
│   └── python/
├── .claude/
├── .codex/
├── .cursor/
└── .trae/
```

## Success Criteria

- 规则层和 skill 层职责清晰，不混杂
- 共享 skill 可直接被新项目复制或同步使用
- 规则可被不同编程工具稳定消费
- 开发、测试、交付和安全流程有统一入口
