# core/src — 源代码目录

## 功能概述
core crate 的源代码实现目录。Codex 的核心逻辑，包括客户端、代理、配置加载等。

## 目录结构
| 文件 | 说明 |
|------|------|
| `api_bridge.rs` | API 桥接层 |
| `apply_patch.rs` | 补丁应用 |
| `arc_monitor.rs` | Arc 引用监控 |
| `auth_env_telemetry.rs` | 认证环境遥测 |
| `client_common.rs` | 客户端公共逻辑 |
| `client.rs` | 客户端实现 |
| `codex_delegate.rs` | Codex 委托 |
| `codex_thread.rs` | Codex 线程 |
| `codex.rs` | Codex 核心 |
| `command_canonicalization.rs` | 命令规范化 |
| `commit_attribution.rs` | 提交归属 |
| `compact.rs` | 上下文压缩 |
| `compact_remote.rs` | 远程压缩 |
| `lib.rs` | 库入口 |
