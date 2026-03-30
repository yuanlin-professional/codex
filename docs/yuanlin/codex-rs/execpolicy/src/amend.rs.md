# `amend.rs` — 策略文件动态追加规则

## 功能概述

提供了在运行时向策略文件追加新规则的功能。支持追加前缀规则（`prefix_rule`）和网络规则（`network_rule`）。实现了基于文件锁的并发安全追加机制，在追加前检查是否已存在相同规则以避免重复。所有操作均为阻塞式 I/O，需要在异步上下文中使用 `tokio::task::spawn_blocking` 调用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AmendError` | 枚举 | 追加操作的错误类型，包含空前缀、无效网络规则、目录创建失败、文件锁定失败、序列化失败等变体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `blocking_append_allow_prefix_rule` | `(policy_path, prefix) -> Result<(), AmendError>` | 向策略文件追加一条允许前缀规则 |
| `blocking_append_network_rule` | `(policy_path, host, protocol, decision, justification) -> Result<(), AmendError>` | 向策略文件追加一条网络规则 |
| `append_rule_line` | `(policy_path, rule) -> Result<()>` | 内部函数：确保目录存在后调用锁定追加 |
| `append_locked_line` | `(policy_path, line) -> Result<()>` | 内部函数：使用文件锁安全追加一行规则，自动去重 |

## 依赖关系

- **引用的 crate**: `serde_json`、`thiserror`
- **被引用方**: `lib.rs` 公开导出，被上层 runtime 调用

## 实现备注

- 使用 advisory file locking 保证并发安全。
- 追加前会读取整个文件内容检查是否已存在相同规则。
- 网络规则的 host 会经过 `normalize_network_rule_host` 标准化处理。
- 文件中包含完整的单元测试，覆盖了目录创建、规则追加、去重、换行处理和网络规则等场景。
