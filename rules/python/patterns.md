---
paths:
  - "**/*.py"
  - "**/*.pyi"
---

# Python Patterns

> This file extends [../common/patterns.md](../common/patterns.md) with Python-specific content.

本文件沉淀 Python 项目里常见的实现模式建议。

## 边界收敛

- 在边界层完成输入校验、模型转换和协议适配
- 在核心逻辑层处理领域规则，而不是一边解析输入一边执行业务
- 外部依赖调用应集中在明确的 client / gateway / service 边界，不要散落全仓库

## 配置集中

- 配置读取统一收敛到单一入口
- 业务模块依赖配置对象，而不是依赖环境变量读取细节
- 新增配置项时，应同步更新配置入口、示例配置和必要文档

## 错误与诊断

- 对外部调用失败，应保留错误类型、关键标识和必要上下文
- 对可重试和不可重试错误应尽量区分，而不是统一包装成模糊异常
- 若项目有结构化日志、request_id 或 trace_id 约定，应在关键路径上保持贯通

## Required Checks

- 新增抽象前，先确认仓库里是否已有现成的 client / gateway / service 模式
- 输入校验、模型转换和业务逻辑是否仍然保持在清晰的层次边界内
- 是否避免把 `dict[str, Any]` 在多个模块之间原样传递
- 外部调用、重试和错误包装是否仍然能保留足够诊断信息
