# `util.rs` — Cloud Tasks 工具函数模块

## 功能概述
提供 cloud-tasks TUI 所需的通用工具函数：User-Agent 后缀设置、错误日志追加、ChatGPT 基础 URL 规范化、JWT 中 account ID 提取、认证 header 构建、任务 URL 生成和相对时间格式化。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `normalize_base_url` | `fn(input) -> String` | 规范化 ChatGPT 基础 URL，自动追加 /backend-api |
| `extract_chatgpt_account_id` | `fn(token) -> Option<String>` | 从 JWT token 中提取 ChatGPT account ID |
| `build_chatgpt_headers` | `async fn() -> HeaderMap` | 构建带认证信息的 HTTP 请求头 |
| `task_url` | `fn(base_url, task_id) -> String` | 生成浏览器友好的任务 URL |
| `format_relative_time` | `fn(reference, ts) -> String` | 格式化相对时间（如"5m ago"） |

## 依赖关系
- **引用的 crate**: `codex_core`, `codex_login`, `reqwest`, `chrono`, `base64`
- **被引用方**: `ui.rs`, `app.rs`, `cli.rs`
