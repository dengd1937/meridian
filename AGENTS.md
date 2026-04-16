# Dev-Agent-Foundry 指南

这是一个**生产就绪的 AI 编程插件**，提供 36 个专用 agent、150 个 skill、68 个命令和自动化 hook 工作流，用于软件开发。

## 核心原则

1. **Agent 优先** — 将领域任务委托给专用 agent
2. **测试驱动** — 先写测试再实现，80%+ 覆盖率要求
3. **安全优先** — 安全不妥协；验证所有输入
4. **不可变性** — 永远创建新对象，禁止原地修改
5. **先规划后执行** — 复杂功能编码前先规划

## 可用 Agent

| Agent | 用途 | 使用时机 |
|---|---|---|
| planner | 实现方案规划 | 复杂功能、重构 |
| tdd-guide | 测试驱动开发 | 新功能、bug 修复 |
| code-reviewer | 代码质量与可维护性 | 写完/修改代码后 |
| security-reviewer | 漏洞检测 | commit 前、安全敏感代码 |
| refactor-cleaner | 清理死代码 | 代码维护 |
| doc-updater | 文档与 codemap | 更新文档时 |
| docs-lookup | 通过 Context7 查文档 | API/文档问题 |
| python-reviewer | Python 代码审查 | Python 项目 |
| typescript-reviewer | TypeScript/React 代码审查 | TypeScript/Next.js 项目 |
| e2e-runner | 浏览器 E2E 测试 | 关键用户流程变更 |

## Agent 编排

在相关场景中向用户建议使用以下 agent（需用户明确触发；commit 前强烈推荐 code-reviewer 和 security-reviewer）：

- 复杂功能需求 → **planner**
- 代码已写完/修改 → **code-reviewer**（commit 前强烈推荐）
- Bug 修复或新功能 → **tdd-guide**
- 安全敏感代码 → **security-reviewer**
- 关键用户流程变更 → **e2e-runner**

独立操作使用并行执行——同时启动多个 agent。

## 安全规范

**任何 commit 前：**

- 无硬编码密钥（API key、密码、token）
- 所有用户输入已验证
- 防 SQL 注入（参数化查询）
- 防 XSS（HTML 已转义）
- CSRF 保护已启用
- 认证/授权已验证
- 所有接口有限流
- 错误信息不泄露敏感数据

**密钥管理：** 禁止硬编码密钥。使用环境变量或密钥管理器。启动时验证必要密钥。立即轮换已暴露的密钥。

**发现安全问题时：** 立即停下 → 使用 security-reviewer agent → 修复 CRITICAL 问题 → 轮换已暴露密钥 → 排查同类问题。

## 编码风格

**不可变性（CRITICAL）：** 永远创建新对象，禁止修改。返回应用了变更的新副本。

**文件组织：** 多小文件优于少大文件。典型 200-400 行，最大 800 行。按功能/领域组织，而非按类型。高内聚、低耦合。

**错误处理：** 每一层都处理错误。UI 代码提供用户友好的信息。服务端记录详细上下文日志。永远不静默吞掉错误。

**输入验证：** 在系统边界验证所有用户输入。使用基于 schema 的验证。快速失败并给出清晰信息。永远不信任外部数据。

**代码质量清单：**

- 函数精简（<50 行），文件聚焦（<800 行）
- 无深层嵌套（>4 层）
- 完善的错误处理，无硬编码值
- 可读、命名清晰

## 测试要求

**最低覆盖率：80%**

必须具备的测试类型：

1. **单元测试** — 独立函数、工具类、组件
2. **集成测试** — API 端点、数据库操作
3. **E2E 测试** — 关键用户流程

**TDD 工作流（强制）：**

1. 先写测试（RED）— 测试应当失败
2. 写最小实现（GREEN）— 测试应当通过
3. 重构（IMPROVE）— 验证覆盖率 80%+

测试失败排查：检查测试隔离 → 验证 mock → 修复实现（而非测试，除非测试本身有误）。

## 开发工作流

完整的 8 步功能实现工作流见 [.agents/rules/development-workflow.md](.agents/rules/development-workflow.md)。

**快速参考：**

1. 调研与复用 → 2. 先规划 → 3. TDD 方式 → 4. 质量门控 → 5. 代码审查 → 6. 文档决策 → 7. Commit & Push → 8. 预审查检查

**关于文档创建：**

- `docs/plans/<feature>.md` 是过程文档，由工作流 Step 2 创建，Step 6 删除——不受「不主动创建文档」限制
- `docs/modules/<module>.md` 需满足条件（新模块/新集成/新技术栈组件）且询问用户后创建

## Git 工作流

**Commit 格式：** `<type>: <description>` — 类型：feat, fix, refactor, docs, test, chore, perf, ci

**PR 工作流：** 分析完整 commit 历史 → 起草完整摘要 → 附上测试计划 → 新分支使用 `-u` 标志推送。

## 架构模式

**API 响应格式：** 统一信封，包含成功标志、数据载荷、错误信息和分页元数据。

**Repository 模式：** 将数据访问封装在标准接口后（findAll, findById, create, update, delete）。业务逻辑依赖抽象接口而非存储机制。

**骨架项目：** 搜索成熟模板，使用并行 agent 评估（安全性、可扩展性、相关性），克隆最优方案，在成熟结构内迭代。

## 验收标准

- 所有测试通过，覆盖率 80%+
- 无安全漏洞
- 代码可读、可维护
- 性能达标
- 满足用户需求
