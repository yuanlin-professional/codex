# `lib.rs` — exec-server crate 入口

## 功能概述

exec-server 库的根模块文件，声明并组织所有内部子模块（client、connection、environment、file_system、local_file_system、local_process、process、process_id、protocol、remote_file_system、remote_process、rpc、server），并通过 `pub use` 统一导出公共类型供外部 crate 使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecServerClient` | re-export | exec-server 客户端 |
| `ExecServerError` | re-export | 错误类型 |
| `Environment` / `EnvironmentManager` | re-export | 执行环境 |
| `ExecBackend` / `ExecProcess` | re-export | 进程执行 trait |
| `ProcessId` | re-export | 进程标识符 |
| 各种 Protocol 类型 | re-export | 协议请求/响应/通知类型 |
| 文件系统类型 | re-export | 文件系统 trait 和选项 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`（大量 Fs* 类型 re-export）
- **被引用方**: 所有使用 `codex_exec_server` crate 的外部代码
