# `lib.rs` — Rollout 持久化与发现 crate 入口

## 功能概述
该文件是 `codex-rollout` crate 的入口模块，负责声明和重导出 rollout 会话文件的持久化与发现功能。定义了会话目录常量（`SESSIONS_SUBDIR`、`ARCHIVED_SESSIONS_SUBDIR`）和交互式会话来源列表 `INTERACTIVE_SESSION_SOURCES`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SESSIONS_SUBDIR` | 常量 | 活跃会话子目录名 `"sessions"` |
| `ARCHIVED_SESSIONS_SUBDIR` | 常量 | 归档会话子目录名 `"archived_sessions"` |
| `INTERACTIVE_SESSION_SOURCES` | 静态列表 | 交互式会话来源列表（Cli, VSCode, atlas, chatgpt） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 重导出 | 多项 | 重导出 `RolloutConfig`, `RolloutRecorder`, `EventPersistenceMode`, `StateDbHandle` 等核心类型 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_login`
- **被引用方**: `codex-core` 及其他上层 crate

## 实现备注
- 使用 `LazyLock` 延迟初始化交互式会话来源列表
