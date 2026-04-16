# Agent 编排

## 可用 Agent

位于 `~/.claude/agents/`：

| Agent | 用途 | 使用时机 |
|-------|------|---------|
| planner | 实现方案规划 | 复杂功能、重构 |
| tdd-guide | 测试驱动开发 | 新功能、bug 修复 |
| code-reviewer | 代码审查 | 写完代码后 |
| security-reviewer | 安全分析 | 提交前 |
| refactor-cleaner | 清理死代码 | 代码维护 |
| docs-lookup | 通过 Context7 查文档 | API/文档问题 |
| doc-updater | 文档更新 | 更新文档时 |
| python-reviewer | Python 代码审查 | Python 项目 |
| typescript-reviewer | TypeScript/React 代码审查 | TypeScript/Next.js 项目 |
| e2e-runner | 浏览器 E2E 测试 | 关键用户流程变更 |
| design-reviewer | 设计产物审查 | 设计交付前（V2-4 最终门控） |

## 推荐触发场景

在相关上下文中向用户建议使用（由用户明确触发；commit 前强烈推荐 code-reviewer 和 security-reviewer）：

1. 复杂功能需求 — 建议使用 **planner** agent
2. 代码已写完/修改 — 建议使用 **code-reviewer** agent _（commit 前强烈推荐）_
3. Bug 修复或新功能 — 建议使用 **tdd-guide** agent
4. 关键用户流程变更 — 建议使用 **e2e-runner** agent
5. 设计产物准备好审查 — 建议使用 **design-reviewer** agent
6. 报告 bug 或出现异常行为 — 优先建议 **investigate** skill
7. 产品想法模糊需要细化 — 建议 **ideate** skill（`/ideate`）

## 关键 Skill

位于 `.agents/skills/`：

| Skill | 用途 | 使用时机 |
|-------|------|---------|
| ideate | 产品想法细化 | 模糊想法 → 结构化产品定义 |
| investigate | 根因分析 | 任何 bug 修复前 |
| design-review | 方案对抗性审查 | 实现前、planner 之后 |
| retro | 任务后复盘 | 功能/修复完成后 |

## 并行任务执行

独立操作务必并行执行：

```
# 正确：并行执行
同时启动 3 个 agent：
1. Agent 1：安全分析 auth 模块
2. Agent 2：性能审查缓存系统
3. Agent 3：工具类类型检查

# 错误：不必要的串行
先 agent 1，再 agent 2，再 agent 3
```

## 多视角分析

对于复杂问题，使用分角色子 agent：
- 事实审查员
- 高级工程师
- 安全专家
- 一致性审查员
- 冗余检查员
