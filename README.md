# dev-agent-foundry

面向现代开发工作流的跨工具 dev-agent 能力底座。

`dev-agent-foundry` 的目标，不是继续维护一个只适用于单一项目或单一工具的 skill 集合，而是把之前在 `sre-copilot` 中验证过的一些通用经验沉淀出来，整理成可复用的 `skills`、`workflows`、`conventions`、`references` 和接入示例，方便新项目直接拿来使用。

设计上会参考 `everything-claude-code`、`superpowers` 等开源仓库的组织思路，但会进一步收敛为更贴合当前开发方式、同时尽量保持工具无关的一套通用资产。

适用工具包括但不限于 Claude Code、Codex、Cursor、Trae，以及其他支持项目级指令文件、rules、prompts 或 agent 协议的 AI 编码工具。

## 愿景

- 从 `sre-copilot` 中提炼可跨项目复用的通用能力
- 将零散经验整理成可组合的 skills、workflows 和 conventions
- 尽量降低对单一工具协议的绑定，提升迁移性
- 让新项目可以快速建立一套可落地的 AI 协作开发基线

## 设计原则

- 通用优先：优先沉淀跨项目、跨技术栈、跨工具都成立的能力
- 工作流优先：不只描述 prompt，还要能服务真实开发闭环
- 工具解耦：把通用能力和工具接入层分开维护
- 渐进接入：允许项目先引入少量能力，再逐步扩展

## 仓库内容

当前仓库的第一批核心沉淀仍然是 `skills`，后续会继续扩展到更多工作流模板、共享 reference 和多工具适配方案。

| 模块 | 作用 | 说明 |
|---|---|---|
| `skills/` | 通用能力协议 | 可复用的开发、审查、交付类能力定义 |
| `references/` | 共享参考材料 | 可被多个 skill 复用的检查清单或规范 |
| `examples/` | 工具接入示例 | 演示如何在不同 AI 编码工具中接入这些能力 |

## 当前 Skills

| Skill | 用途 | 说明 |
|---|---|---|
| [git-workflow](skills/git-workflow/SKILL.md) | 交付工作流 | commit / push / PR / CI / merge 全流程闭环 |
| [code-review-expert](skills/code-review-expert/SKILL.md) | 独立代码审查 | findings 驱动的结构化 review 协议 |
| [self-review](skills/self-review/SKILL.md) | 本地自审 | 提交前的作者视角质量验证 |
| [design-review](skills/design-review/SKILL.md) | 设计方案审查 | 完整性、可行性、安全性、可维护性四维度 |
| [doc-gardening](skills/doc-gardening/SKILL.md) | 文档园艺 | 检测并修复文档与代码之间的漂移 |

## 通用接入模式

无论具体工具是什么，接入 `dev-agent-foundry` 时都尽量遵循同一个模式：

1. 选择需要的通用能力
2. 将对应 skill 复制或同步到项目中
3. 通过工具自己的项目级指令机制注册这些能力
4. 在项目级文档中保留一层工具无关的解释，避免未来迁移时重做

一个实用的最小接入方式是：

- 把共享能力放在 `.agents/skills/`
- 使用 `AGENTS.md`、`CLAUDE.md`、`.cursor/rules/` 或其他工具机制做接入层
- 项目特定规则继续留在本项目自己的指令文件里

## 按工具接入

| 工具 | 推荐接入方式 | 示例 |
|---|---|---|
| Claude Code | `.agents/skills/` + `CLAUDE.md` | [examples/claude-code/README.md](examples/claude-code/README.md) |
| Codex | 项目内 skill 文档 + `AGENTS.md` | [examples/codex/README.md](examples/codex/README.md) |
| Cursor | `.cursor/rules/` + 项目内 skill 文档，`AGENTS.md` 可作为共享入口 | [examples/cursor/README.md](examples/cursor/README.md) |
| Trae | 项目级 instructions/rules 机制 + 项目内 skill 文档 | [examples/trae/README.md](examples/trae/README.md) |

工具接入总览见 [examples/README.md](examples/README.md)。

## 使用方式

### 方式一：直接复制

将需要的 skill 目录复制到项目中，再按项目实际情况调整。

```bash
# 复制单个 skill
cp -r skills/git-workflow /your-project/.agents/skills/

# 复制全部 skill
cp -r skills/* /your-project/.agents/skills/
```

### 方式二：Git submodule

```bash
cd /your-project
git submodule add https://github.com/dengd1937/dev-agent-foundry.git .agents/dev-agent-foundry
```

### 方式三：手动同步

定期从本仓库拉取更新，再合并到项目自己的 agent 目录中。适合希望保留深度定制能力的团队。

## 项目适配

Skill 设计为项目无关，但部分能力会引用项目级文件或既有规范。

适配时重点关注：

1. `Agent` 指令文件
   例如 `CLAUDE.md`、`AGENTS.md`、`.cursor/rules/*.mdc` 或项目自定义 instructions 文件
2. Git 流程文档
   `git-workflow` 会优先查找项目内已有的 Git 规范文档
3. 技术栈 references
   `code-review-expert` 的 references 按语言或框架分组，可按需扩展

## 目录结构

```text
dev-agent-foundry/
├── skills/
│   ├── git-workflow/          # 交付工作流
│   ├── code-review-expert/    # 独立代码审查
│   ├── design-review/         # 设计方案审查
│   ├── self-review/           # 本地自审
│   └── doc-gardening/         # 文档园艺
├── references/                # 跨 skill 共享参考材料
└── examples/                  # 多工具接入示例
    ├── claude-code/
    ├── codex/
    ├── cursor/
    └── trae/
```

## 演进方向

- 补充更多跨语言、跨技术栈的 review references
- 沉淀更完整的 workflow 模板和多 agent 协作模式
- 增加更多工具接入示例和迁移指南
- 将在 `sre-copilot` 中验证有效的通用约束继续拆分沉淀

## 贡献

欢迎提交 Issue 和 PR。新增能力时，优先考虑：

- 是否具备跨项目复用价值
- 是否能够脱离单一工具独立成立
- 是否适合作为通用协议，而不是项目私有规则

## License

MIT
