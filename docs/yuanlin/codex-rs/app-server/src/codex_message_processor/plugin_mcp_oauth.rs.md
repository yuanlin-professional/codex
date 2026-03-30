# `codex_message_processor/plugin_mcp_oauth.rs` — 插件 MCP 服务器静默 OAuth 登录

## 功能概述

本模块为 `CodexMessageProcessor` 实现了 `start_plugin_mcp_oauth_logins` 方法，用于在插件安装过程中对需要 OAuth 认证的 MCP 服务器执行静默登录。对于每个 MCP 服务器，它会检查是否支持 OAuth 登录，解析 scope，然后在后台 tokio 任务中异步执行静默登录流程。如果首次带 scope 登录失败且满足重试条件，会尝试不带 scope 再次登录。最终结果通过 `McpServerOauthLoginCompleted` 通知发送给客户端。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 为 `CodexMessageProcessor` 添加方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_plugin_mcp_oauth_logins` | `async fn(&self, config, plugin_mcp_servers)` | 为每个需要 OAuth 的 MCP 服务器启动后台静默登录任务 |

## 依赖关系

- **引用的 crate**: `codex_core::mcp::auth`（`oauth_login_support`、`resolve_oauth_scopes`）、`codex_rmcp_client`（`perform_oauth_login_silent`）
- **被引用方**: `codex_message_processor.rs`（处理插件安装时调用）

## 实现备注

- 每个 MCP 服务器的登录在独立的 `tokio::spawn` 任务中运行，互不阻塞。
- 使用 `should_retry_without_scopes` 判断是否需要在无 scope 情况下重试。
