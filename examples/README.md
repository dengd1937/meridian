# Examples

这里收集 `dev-agent-foundry` 在不同 AI 编码工具中的接入示例。

这些示例的目标不是覆盖每个产品的全部特性，而是提供一套稳定的最小接入模式，让项目可以先把通用能力落地，再逐步做工具专属增强。

## 通用思路

无论使用哪种工具，建议都先把接入拆成两层：

1. 通用能力层
   共享的 `skills`、`references`、工作流协议，尽量与具体工具解耦
2. 工具适配层
   使用工具自己的规则文件、指令文件或 prompt 机制，把这些能力挂接进去

推荐保留一个项目内的共享位置，例如：

- `.agents/skills/`
- `docs/agents/`
- 其他团队约定的版本化目录

## 工具矩阵

| 工具 | 核心接入点 | 说明 |
|---|---|---|
| [Claude Code](claude-code/README.md) | `CLAUDE.md` | 适合把 skill 注册为项目级能力 |
| [Codex](codex/README.md) | `AGENTS.md` | 适合用 `AGENTS.md` 组织项目规则和能力入口 |
| [Cursor](cursor/README.md) | `.cursor/rules/` | 适合用项目规则把通用 skill 挂接到 Agent，`AGENTS.md` 可作为共享入口 |
| [Trae](trae/README.md) | 项目 instructions / rules 机制 | 建议保留工具无关的共享文档层，再做工具接入 |

## 接入建议

- 先接入最常用的 2 到 3 个 skill，不要一开始全量塞入
- 把项目私有约束和通用 skill 分开维护，降低后续迁移成本
- 尽量保留一个工具无关的总入口文档，例如 `AGENTS.md`
- 如果某个工具支持更强的 rules 或 metadata 机制，再在适配层补充，不要污染通用 skill 本身
