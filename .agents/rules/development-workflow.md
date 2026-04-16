# 开发工作流

> 本文件是 [git-workflow.md](./git-workflow.md) 的延伸，描述 git 操作之前的完整功能开发流程。

功能实现工作流涵盖开发全流程：调研、规划、TDD、代码审查，最后提交到 git。

## 功能实现工作流

1. **调研与复用** _(任何新实现前必须执行)_
   - **若 `docs/product/<feature>.md` 存在**，优先阅读：MVP 范围、功能优先级、竞品分析和技术约束均来自 PM 阶段
   - **若 `docs/designs/<feature>/` 存在**，调用 **design-workflow** skill，按 V2-5 交付表读取设计产物（intent.md、screenshots、review-verdict.md）
   - **优先 GitHub 代码搜索：** 使用 GitHub MCP 搜索现有实现、模板和模式，再动手写新代码
   - **其次查官方文档：** 使用 Context7 或官方文档确认 API 行为、包用法和版本细节
   - **行为不确定时用 docs-lookup：** 框架、API 或第三方库行为不确定时，实现前先运行 **docs-lookup** agent
   - **前两项不够用再用 Exa：** GitHub 搜索和官方文档之后，再用 Exa 进行更广泛的网络调研
   - **检查包注册表：** 写工具代码前先搜索 npm、PyPI、crates.io 等；优先使用成熟库而非手写
   - **搜索可复用实现：** 寻找能解决 80%+ 问题的开源项目，考虑 fork、移植或封装
   - 在满足需求的前提下，优先采用或移植已验证的方案，而非从零编写

2. **先规划**
   - 使用 **planner** agent 制定实现方案
   - 编码前将规划文档保存到 `docs/plans/<feature-name>.md`
   - **若 `docs/designs/<feature>/` 存在**，调用 **design-workflow** skill，在方案中按 V2-5 交付表引用组件契约和审查结论
   - 识别依赖和风险
   - 拆解为多个阶段
   - **[强制门控]** 向用户展示方案并等待明确批准——**用户批准前不得写任何代码**
   - _可选：_ 复杂方案可在批准前运行 **design-review** skill 进行对抗性审查（也可使用 **design-review-codex** 进行跨模型独立分析）

3. **TDD 方式**
   - 修 bug 时，先运行 **investigate** skill 再写修复代码
   - 使用 **tdd-guide** agent
   - **若 `docs/designs/<feature>/` 存在**，调用 **design-workflow** skill，按 V2-5 交付表验证 token 使用及文档中的状态、变体和无障碍要求
   - 先写测试（RED）
   - 写实现让测试通过（GREEN）
   - 重构（IMPROVE）
   - 关键用户流程变更：添加/更新 E2E 覆盖并运行 **e2e-runner** agent
   - 验证覆盖率 80%+

4. **质量门控** _(编辑文件后)_
   - 写完或修改源文件后运行 **code-quality-gate** skill
   - 格式化所有修改过的文件（Biome/Prettier/Ruff/Black）
   - Lint 并自动修复问题（Biome/ESLint/Ruff）
   - 运行类型检查器（tsc/mypy）
   - 清除调试产物（console.log、debugger、print 语句）
   - 修复所有错误后再进入代码审查

5. **代码审查** _(commit 前)_
   - 质量门控通过后运行 **code-reviewer** agent；跳过时 `pre-commit-review-check` hook 会提醒
   - Python 项目：同时运行 **python-reviewer**
   - TypeScript/Next.js：同时运行 **typescript-reviewer**
   - auth、输入验证、公开接口、HTML 渲染、文件上传或密钥/env 处理：同时运行 **security-reviewer**
   - commit 前修复 CRITICAL 和 HIGH 问题；MEDIUM 问题酌情处理
   - 通过其他方式完成审查时可跳过 hook 提醒：`export SKIP_REVIEW_CHECK=1`

6. **文档决策** _(commit 前)_
   - 删除 `docs/plans/<feature-name>.md` — 规划文档是过程产物，非长期知识
   - 询问用户后创建/更新 `docs/modules/<module>.md`，条件如下（满足任一即可）：
     - 新独立模块（新 package / Django app）
     - 新外部集成（第三方 API 或服务）
     - 新技术栈组件（新数据库、消息队列等）
   - 若创建了模块文档，同步更新 `docs/index.md` 中的模块表
   - 以上条件均不满足时，确保代码注释足够清晰——无需创建新文档

7. **Commit & Push**
   - commit 前运行 **commit-quality** skill
   - 验证 commit message 格式（Conventional Commits）
   - 仅 lint 暂存文件
   - 调试产物和密钥由 pre-write / pre-bash hook 自动捕获
   - 禁止使用 `--no-verify` — hook 会阻断；应修复失败的检查而非绕过
   - commit message 格式和 PR 流程见 [git-workflow.md](./git-workflow.md)

8. **预审查检查**
   - 确认所有自动化检查（CI/CD）已通过
   - 解决所有 merge 冲突
   - 确保分支已与目标分支同步
   - 以上全部满足后再请求代码审查
