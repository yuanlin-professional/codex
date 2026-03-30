# `constraint.rs` — 受约束值类型系统

## 功能概述

实现通用的受约束值类型 `Constrained<T>`，为配置系统提供运行时值验证和规范化能力。每个 `Constrained` 实例持有一个当前值、一个验证器（validator）闭包和一个可选的规范化器（normalizer）闭包。验证器在 `set` 和 `can_set` 时被调用以确保候选值满足约束，规范化器在值存储前对其进行变换。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Constrained<T>` | 泛型结构体 | 受约束值容器，封装值、验证器和可选规范化器 |
| `ConstraintError` | 枚举 | 约束违反错误：`InvalidValue`（值不在允许集合中）、`EmptyField`（字段为空）、`ExecPolicyParse`（执行策略解析失败） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Constrained::new` | `(initial_value, validator) -> ConstraintResult<Self>` | 创建带验证器的受约束值，初始值必须通过验证 |
| `Constrained::normalized` | `(initial_value, normalizer) -> ConstraintResult<Self>` | 创建带规范化器的受约束值（验证器允许任何值） |
| `Constrained::allow_any` | `(initial_value) -> Self` | 创建无约束的值容器 |
| `Constrained::allow_only` | `(only_value) -> Self` | 创建仅允许特定值的约束 |
| `Constrained::allow_any_from_default` | `() -> Self` | 创建无约束的值容器，初始值为 `T::default()` |
| `Constrained::get` / `value` | 获取当前值的引用 / 拷贝 |  |
| `Constrained::can_set` | `(&self, candidate) -> ConstraintResult<()>` | 探测候选值是否满足约束，不修改当前值 |
| `Constrained::set` | `(&mut self, value) -> ConstraintResult<()>` | 设置新值，先规范化后验证，失败则保留原值 |

## 依赖关系

- **引用的 crate**: `thiserror`、`std::sync::Arc`
- **被引用方**: `config_requirements.rs` 中的 `ConfigRequirements` 大量使用 `Constrained` 和 `ConstrainedWithSource`

## 实现备注

- 验证器和规范化器存储为 `Arc<dyn Fn>` 以支持 `Clone`，但 `PartialEq` 仅比较内部值。
- `ConstraintError` 实现了 `From<ConstraintError> for std::io::Error`，方便在 I/O 上下文中传播。
- 包含完整的单元测试覆盖各种约束场景。
