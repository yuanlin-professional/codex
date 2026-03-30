# `lib.rs` — codex-core 库根模块

## 功能概述

此文件是 `codex-core` 库的根模块，负责声明和组织所有子模块，并通过 `pub use` 进行选择性的公开导出。它定义了 codex-core 的公共 API 表面，包括模型客户端、会话管理、配置系统、执行引擎、技能系统、回滚存储等核心组件。文件还通过 `#![deny(clippy::print_stdout, clippy::print_stderr)]` 禁止库代码直接输出到 stdout/stderr。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexThread` | 重导出 | 会话线程，从 `codex_thread` 导出 |
| `ThreadManager` | 重导出 | 线程管理器，从 `thread_manager` 导出 |
| `ModelClient` | 重导出 | 模型客户端，从 `client` 导出 |
| `RolloutRecorder` | 重导出 | 回滚记录器，从 `rollout` 导出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| （此文件主要是模块声明和导出，无独立函数） | — | — |

## 依赖关系

- **引用的 crate**: 几乎所有 `codex-*` 子 crate，包括 `codex_protocol`、`codex_login`、`codex_analytics`、`codex_sandboxing`、`codex_tools`、`codex_shell_command`、`codex_rollout`、`codex_utils_path` 等
- **被引用方**: 作为核心库被 `codex-cli`、`codex-tui`、`codex-app-server` 等上层应用依赖

## 实现备注

- 提供了向后兼容的废弃别名：`ConversationManager`（改用 `ThreadManager`）、`NewConversation`（改用 `NewThread`）、`CodexConversation`（改用 `CodexThread`）
- `default_client` 模块通过转发层实现，实际代码在 `codex_login::default_client` 中
- `mentions` 模块是 `pub(crate)` 可见性的内部聚合模块
- `compact` 和 `memory_trace` 模块是公开的，供外部直接使用
- `skills` 相关类型大量使用 `pub(crate)` 导出，限制在 crate 内部使用
