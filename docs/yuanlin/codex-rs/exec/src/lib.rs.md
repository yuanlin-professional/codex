# `lib.rs` — codex-exec 核心运行逻辑

## 功能概述

codex-exec crate 的主入口库文件，包含完整的非交互式 Codex 代理会话运行逻辑。负责 CLI 参数解析后的配置构建、OSS 提供者就绪检查、OpenTelemetry 初始化、in-process app-server 客户端启动、会话创建/恢复、事件循环处理以及优雅关闭。支持普通用户 turn、代码审查和恢复先前会话三种初始操作。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `InitialOperation` | enum | 初始操作类型：`UserTurn`（用户消息）或 `Review`（代码审查） |
| `StdinPromptBehavior` | enum | stdin 读取行为：RequiredIfPiped、Forced、OptionalAppend |
| `RequestIdSequencer` | struct | 自增请求 ID 序列器 |
| `ExecRunArgs` | struct | 运行 exec 会话所需的全部参数集合 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_main` | `async (cli, arg0_paths) -> Result<()>` | 公开入口，配置构建、OTel 初始化、启动 exec 会话 |
| `run_exec_session` | `async (args) -> Result<()>` | 核心事件循环：创建/恢复线程、发送初始操作、处理通知直至关闭 |
| `resolve_resume_thread_id` | `async (client, config, args) -> Result<Option<String>>` | 通过 thread/list API 按 session_id 或 --last 查找可恢复的线程 |
| `handle_server_request` | `async (client, request, error_seen)` | 处理服务端请求（拒绝审批、elicitation 等，exec 模式不支持交互） |
| `maybe_backfill_turn_completed_items` | `async (client, request_ids, notification)` | turn 完成时若 items 为空，通过 thread/read 回填 |
| `resolve_root_prompt` | `(prompt_arg) -> String` | 解析 prompt：支持参数、stdin 管道、`-` 强制 stdin |
| `decode_prompt_bytes` | `(input) -> Result<String, PromptDecodeError>` | 解码 prompt 字节：支持 UTF-8/UTF-16 BOM，拒绝 UTF-32 |
| `build_review_request` | `(args) -> Result<ReviewRequest>` | 构建代码审查请求 |

## 依赖关系

- **引用的 crate**: `codex_app_server_client`、`codex_app_server_protocol`、`codex_core`、`codex_protocol`、`codex_arg0`、`codex_feedback`、`codex_git_utils`、`codex_otel`、`codex_cloud_requirements`、`codex_utils_cli`、`codex_utils_oss`、`codex_utils_absolute_path`
- **被引用方**: `exec/src/main.rs`

## 实现备注

- 所有 stdout 输出被 `#![deny(clippy::print_stdout)]` 禁止，确保仅通过 EventProcessor 写入。
- exec 模式下所有服务端审批请求（命令执行、文件变更、权限等）均被自动拒绝。
- MCP elicitation 请求被自动取消（cancel action）。
- 支持 UTF-16LE/BE BOM 的 stdin prompt 解码。
