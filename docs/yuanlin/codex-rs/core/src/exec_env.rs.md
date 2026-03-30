# `exec_env.rs` — Shell 执行环境变量构建

## 功能概述

此文件根据 `ShellEnvironmentPolicy` 配置构建子进程的环境变量映射。它实现了一套分层的环境变量过滤算法：从继承策略出发，依次应用默认排除（敏感变量）、自定义排除、用户覆盖、仅包含过滤，最后注入线程 ID。结果可直接传递给 `Command::envs()` 使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CODEX_THREAD_ID_ENV_VAR` | 常量 | 线程 ID 环境变量名 `"CODEX_THREAD_ID"` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_env` | `(policy, thread_id) -> HashMap<String, String>` | 从当前进程环境和策略构建环境变量映射 |
| `populate_env` | `(vars, policy, thread_id) -> HashMap<String, String>` | 内部实现，接受可迭代的变量来源（便于测试） |

## 依赖关系

- **引用的 crate**: `crate::config::types`（`ShellEnvironmentPolicy`、`EnvironmentVariablePattern`）、`codex_protocol`（`ThreadId`）
- **被引用方**: `lib.rs` 公开导出此模块，`spawn.rs` 等使用

## 实现备注

- 继承策略三种模式：`All`（继承全部）、`None`（不继承）、`Core`（仅继承 HOME、PATH、SHELL 等核心变量）
- 默认排除匹配包含 KEY、SECRET、TOKEN 的变量名（大小写不敏感）
- Windows 下核心变量匹配也是大小写不敏感的
- `CODEX_THREAD_ID` 在 `include_only` 过滤之后注入，确保始终可用
- 算法按步骤严格执行：继承 -> 默认排除 -> 自定义排除 -> set 覆盖 -> include_only 过滤 -> 线程 ID
