# `session_rollout_init_error.rs` — 会话初始化错误映射

## 功能概述

该文件提供会话初始化过程中 I/O 错误到用户友好错误信息的映射功能。当 Codex 在初始化会话时遇到文件系统相关错误（如权限拒绝、路径不存在、路径已存在、数据损坏、路径类型不匹配等），该模块会生成包含具体修复建议的错误消息，帮助用户快速定位和解决问题。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `map_session_init_error` | `fn(err: &anyhow::Error, codex_home: &Path) -> CodexErr` | 遍历错误链，尝试将 I/O 错误映射为带有用户提示的 `CodexErr::Fatal`；若无法映射则返回通用错误信息 |
| `map_rollout_io_error` | `fn(io_err: &std::io::Error, codex_home: &Path) -> Option<CodexErr>` | 根据 `ErrorKind`（`PermissionDenied`、`NotFound`、`AlreadyExists`、`InvalidData`、`IsADirectory` 等）生成对应的修复提示信息 |

## 依赖关系

- **引用的 crate**: `std::io::ErrorKind`、`std::path::Path`、`anyhow::Error`
- **引用的模块**: `crate::error::CodexErr`、`crate::rollout::SESSIONS_SUBDIR`
- **被引用方**: 会话初始化流程中的错误处理路径

## 实现备注

错误映射使用 `anyhow::Error` 的 `chain()` 方法遍历整个错误链，通过 `filter_map` 和 `find_map` 组合查找第一个可映射的 `std::io::Error`。每种错误类型都附带了具体的路径信息和操作建议（如使用 `chown` 修复权限问题）。
