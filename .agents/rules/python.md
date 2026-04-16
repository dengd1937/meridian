# Python 规范

## 编码风格

- 遵循 **PEP 8** 规范；所有函数签名使用**类型注解**
- 优先使用不可变数据结构：`@dataclass(frozen=True)`、`NamedTuple`
- 格式化工具：**black** + **isort** + **ruff**

## 模式

- **Protocol** 实现鸭子类型（结构子类型）
- **Dataclass** 作为 DTO（`@dataclass`）
- **上下文管理器** 管理资源；**生成器** 实现懒迭代

> 详见 skill: `python-patterns`

## 安全

- 使用 `os.environ["KEY"]`（缺失时抛出 `KeyError`）— 禁止静默默认值
- 用 `python-dotenv` 加载 `.env`；启动时验证所有必要密钥是否存在

> 详见 skill: `django-security`

## 测试

- 框架：**pytest**
- 覆盖率：`pytest --cov=src --cov-report=term-missing`
- 用 `pytest.mark.unit` / `pytest.mark.integration` 分类

> 详见 skill: `python-testing`
