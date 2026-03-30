# `apps_test_server.rs` — Codex Apps 模拟测试服务器

## 功能概述

提供一个基于 wiremock 的模拟 Codex Apps 服务器 (`AppsTestServer`)，用于集成测试中模拟 ChatGPT Apps/Connectors 的 Streamable HTTP JSON-RPC 后端。该服务器模拟了 OAuth 元数据端点、连接器目录列表端点以及 MCP JSON-RPC 端点（支持 `initialize`、`tools/list`、`tools/call` 等方法）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppsTestServer` | struct | 模拟 Apps 服务器句柄，持有 `chatgpt_base_url`，提供 `mount()`、`mount_searchable()`、`mount_with_connector_name()` 等异步构建方法 |
| `CodexAppsJsonRpcResponder` | struct | 实现 `wiremock::Respond` trait 的 JSON-RPC 应答器，根据 `method` 字段路由到 `initialize`/`tools/list`/`tools/call` 等处理分支 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `AppsTestServer::mount` | `async fn mount(server: &MockServer) -> Result<Self>` | 使用默认连接器名称 "Calendar" 挂载所有 mock 端点 |
| `AppsTestServer::mount_searchable` | `async fn mount_searchable(server: &MockServer) -> Result<Self>` | 挂载可搜索版本，`tools/list` 返回 100 个工具 |
| `mount_oauth_metadata` | `async fn(server: &MockServer)` | 挂载 `/.well-known/oauth-authorization-server/mcp` 端点 |
| `mount_connectors_directory` | `async fn(server: &MockServer)` | 挂载 `/connectors/directory/list` 和 `list_workspace` 端点 |
| `mount_streamable_http_json_rpc` | `async fn(server: &MockServer, ...)` | 挂载 `/api/codex/apps` 的 POST 处理，使用 `CodexAppsJsonRpcResponder` |

## 依赖关系

- **引用的 crate**: `wiremock`、`serde_json`、`anyhow`
- **被引用方**: 被 `codex-core` 集成测试中的 Apps 功能测试使用

## 实现备注

- `tools/list` 在 `searchable` 模式下会在基本 2 个工具之外额外生成 98 个 `calendar_timezone_option_{index}` 工具，模拟大规模工具列表场景
- `tools/call` 响应中会回传请求中的 `_meta._codex_apps` 字段到 `structuredContent` 中
- 使用固定的常量 `CONNECTOR_ID`、`PROTOCOL_VERSION` 等确保测试一致性
