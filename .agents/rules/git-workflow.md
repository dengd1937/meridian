# Git 工作流

## Commit Message 格式

```
<type>: <description>

<optional body>
```

类型：feat, fix, refactor, docs, test, chore, perf, ci

注：署名已通过 `~/.claude/settings.json` 全局禁用。

## Pull Request 工作流

创建 PR 时：
1. 分析完整 commit 历史（不只是最新 commit）
2. 使用 `git diff [base-branch]...HEAD` 查看所有变更
3. 起草完整的 PR 摘要
4. 附上含 TODO 的测试计划
5. 新分支时使用 `-u` 标志推送

合并 PR 时：
- 务必使用 `--delete-branch` 选项：`gh pr merge <pr-number> --squash --delete-branch`

> git 操作前的完整开发流程（规划、TDD、代码审查）见 [development-workflow.md](./development-workflow.md)。
