# Rules

`rules/` 用于沉淀跨项目复用的默认规则。

这里参考 `everything-claude-code` 的思路，将“规则”与“skill”分开：

- `rules/` 回答应该遵守什么
- `.agents/skills/` 回答遇到任务时怎么做

## 当前结构

- `common/`：尽量适用于大多数项目的通用规则
- `python/`：Python 项目的扩展规则
- 当前暂不单独维护 `hooks/`，工具执行后的自动化动作放在工具适配层中处理
- 后续可继续增加更多语言、框架或技术栈专属规则目录

## 当前主题

- [common/coding-style.md](common/coding-style.md)
- [common/patterns.md](common/patterns.md)
- [common/documentation.md](common/documentation.md)
- [common/configuration.md](common/configuration.md)
- [common/git-workflow.md](common/git-workflow.md)
- [common/testing.md](common/testing.md)
- [common/performance.md](common/performance.md)
- [common/code-review.md](common/code-review.md)
- [common/security.md](common/security.md)
- [common/agents.md](common/agents.md)

## 使用原则

- 这些规则默认作为项目级基线
- 如果目标项目已有更具体、更新更近的规则，应优先服从项目规则
- 若某条规则明显依赖特定技术栈或组织流程，应拆到更具体的规则层，而不是留在 `common/`
- 当语言层规则与 `common/` 冲突时，语言层规则优先

## 分层原则

- `common/` 只保留语言无关的默认原则，不放强绑定语言工具链的细节
- 语言目录在对应主题文件中扩展 `common/`，例如 `python/coding-style.md` 扩展 `common/coding-style.md`
- 项目接入时，应先加载 `common/`，再加载与技术栈匹配的语言目录
- 若语言层与 `common/` 冲突，语言层优先
- 若某条规则需要自动路由到特定文件类型，优先在语言层使用 front matter 等轻量元数据表达

## 当前语言层入口

- [python/coding-style.md](python/coding-style.md)
- [python/testing.md](python/testing.md)
- [python/security.md](python/security.md)
- [python/patterns.md](python/patterns.md)

## 接入方式

### 安装原则

- `common/` 应作为所有项目的默认基线
- 语言层目录按项目技术栈叠加安装，例如 Python 项目安装 `common/ + python/`
- 保留目录层级，不要把不同目录下的同名文件拍平成一个目录

### 在本仓库中的使用方式

- 共享 skill 真源在 `.agents/skills/`
- `.claude/`、`.cursor/`、`.trae/` 只负责工具接入，不应成为规则或 skill 的真源
- 项目级接入时，应先从 `AGENTS.md` 建立导航，再按任务类型加载 `rules/` 和相关 skill

## Rules vs Skills

- `rules/` 定义标准、约束、完成态和检查项
- `.agents/skills/` 提供具体任务的执行协议和操作路径
- 更合适放进 skill 的内容，不应堆回 `rules/`
