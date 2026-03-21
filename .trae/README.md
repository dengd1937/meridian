# Trae Adapter

`.trae/` 是 Trae 的适配层。

共享 skill 真源不放在这里，而是通过 `skills -> ../.agents/skills` 软链接接入。

当前已补的适配蓝图：

- `.trae/planner.md`
- `.trae/tdd-guide.md`
- `.trae/code-review-expert.md`

当前先使用蓝图文档说明这些共享 skill 如何映射到 Trae 的 `Rules + Agent Skills` 体系；等 Trae 公布更稳定的项目级仓库格式后，再升级为自动加载入口。
