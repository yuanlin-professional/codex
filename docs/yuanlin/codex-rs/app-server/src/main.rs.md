# `main.rs` — app-server 二进制入口

## 功能概述

本文件是 `codex-app-server` 可执行文件的入口点。它使用 `clap` 解析命令行参数（`--listen` 传输 URL、`--session-source` 会话来源、WebSocket 认证参数），然后通过 `arg0_dispatch_or_else` 处理 argv0 分发后调用 `run_main_with_transport` 启动 app-server。支持通过 `CODEX_APP_SERVER_MANAGED_CONFIG_PATH` 环境变量（仅 debug 构建）指定托管配置文件路径以便集成测试。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerArgs` | 结构体（clap） | 命令行参数定义：`--listen`、`--session-source`、认证子参数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `fn() -> anyhow::Result<()>` | 程序入口 |
| `managed_config_path_from_debug_env` | `fn() -> Option<PathBuf>` | 从环境变量读取调试用托管配置路径 |

## 依赖关系

- **引用的 crate**: `codex_app_server`（`run_main_with_transport`、`AppServerTransport`）、`codex_arg0`、`clap`
- **被引用方**: 无（最终二进制）

## 实现备注

- 默认传输为 `stdio://`，默认会话来源为 `vscode`。
- `managed_config_path_from_debug_env` 仅在 `debug_assertions` 启用时生效。
