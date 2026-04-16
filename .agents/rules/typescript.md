# TypeScript/JavaScript 规范

## 编码风格

### 类型与接口

- 为导出函数、共享工具类和公共类方法添加参数和返回值类型
- `interface` 用于可扩展的对象结构；`type` 用于联合类型、交叉类型、映射类型
- 优先使用字符串字面量联合类型而非 `enum`；避免 `any`，外部输入用 `unknown` 再安全收窄
- 组件 props 使用具名 `interface` 或 `type`；不使用 `React.FC`

### 关键模式

- **不可变性**：用展开运算符更新，禁止修改现有对象
- **错误处理**：async/await + try-catch；用 `instanceof Error` 收窄 `unknown` 类型
- **输入验证**：Zod 做基于 schema 的验证；用 `z.infer<typeof schema>` 推导类型
- **Tailwind v4**：仅用语义类（`bg-primary`、`rounded-md`），仅用 CSS `@theme` 块，合并用 `cn()`（来自 `@/lib/utils`）
- **shadcn/ui**：从 `@/components/ui/` 导入，仅用 Lucide 图标，变体用 CVA

> 详见 skill: `typescript-patterns`

## 模式

- `ApiResponse<T>` 信封包裹所有 API 响应（success、data、error、meta）
- 自定义 hook 封装可复用状态逻辑，使用显式泛型类型
- Repository 模式配合类型化接口
- shadcn/ui 组合：`cn()` 合并类名，CVA 管理变体，仅用语义 Tailwind token

> 详见 skill: `typescript-patterns`

## 安全

- 使用 `process.env.KEY` 并在启动时显式验证——缺失时抛出异常，禁止静默默认为 undefined

> 详见 skill: `security-reviewer`

## 测试

- **单元/组件测试**：Vitest + React Testing Library
- **E2E**：Playwright；基准截图提交到仓库
- **API Mock**：MSW — 禁止直接 mock fetch/axios 实现
- **无障碍**：`@axe-core/playwright` 检查 WCAG 2.1 AA
- 选择器优先级：`getByRole` > `getByLabelText` > `getByText` > `getByTestId`
- 测试用户可见行为，而非实现细节

**反模式（禁止）：**
- 测试 mock 行为而非真实功能
- 为生产代码添加仅测试使用的方法
- 在 `.forEach` 中使用异步断言
- 用快照替代行为断言
- 依赖测试执行顺序

> 详见 skill: `typescript-testing`
