# `auth.rs` — MCP OAuth 认证支持

## 功能概述

`auth.rs` 实现了 MCP（Model Context Protocol）服务器的 OAuth 认证发现与状态计算。它负责探测 StreamableHttp 类型的 MCP 服务器是否支持 OAuth 登录、解析 OAuth scope 的优先级（显式指定 > 配置 > 发现 > 空）、判断是否应在 scope 被拒绝后重试、以及批量计算多个 MCP 服务器的认证状态。该模块是 MCP 连接建立前的认证准备层。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `McpOAuthLoginConfig` | struct | OAuth 登录所需的配置信息：URL、HTTP 头、发现的 scope 列表 |
| `McpOAuthLoginSupport` | enum | OAuth 登录支持状态：`Supported`（已支持）、`Unsupported`（不支持）、`Unknown`（探测失败） |
| `McpOAuthScopesSource` | enum | OAuth scope 来源：`Explicit`、`Configured`、`Discovered`、`Empty` |
| `ResolvedMcpOAuthScopes` | struct | 解析后的 OAuth scope 及其来源 |
| `McpAuthStatusEntry` | struct | 单个 MCP 服务器的配置与认证状态 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `oauth_login_support` | `async fn oauth_login_support(transport) -> McpOAuthLoginSupport` | 探测指定传输配置是否支持 OAuth 登录 |
| `discover_supported_scopes` | `async fn discover_supported_scopes(transport) -> Option<Vec<String>>` | 发现 MCP 服务器支持的 OAuth scope |
| `resolve_oauth_scopes` | `fn resolve_oauth_scopes(explicit, configured, discovered) -> ResolvedMcpOAuthScopes` | 按优先级解析最终使用的 OAuth scope |
| `should_retry_without_scopes` | `fn should_retry_without_scopes(scopes, error) -> bool` | 判断是否应在 scope 被 provider 拒绝后不带 scope 重试 |
| `compute_auth_statuses` | `async fn compute_auth_statuses(servers, store_mode) -> HashMap<...>` | 并发计算多个 MCP 服务器的认证状态 |
| `compute_auth_status` | `async fn compute_auth_status(server_name, config, store_mode) -> Result<McpAuthStatus>` | 计算单个 MCP 服务器的认证状态 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_rmcp_client`, `anyhow`, `futures`, `tracing`
- **引用的内部模块**: `McpServerConfig`, `McpServerTransportConfig`
- **被引用方**: `mcp/mod.rs`（用于 `collect_mcp_snapshot`）、`mcp/skill_dependencies.rs`（用于依赖安装时的 OAuth 登录）

## 实现备注

- 仅 `StreamableHttp` 传输类型支持 OAuth，`Stdio` 类型直接返回 `Unsupported`
- 如果已配置 `bearer_token_env_var`，则跳过 OAuth 发现（使用静态 token）
- `should_retry_without_scopes` 仅对 `Discovered` 来源的 scope 允许重试，避免覆盖用户显式配置
- `compute_auth_statuses` 使用 `join_all` 并发探测所有服务器，失败时降级为 `Unsupported`
- 内嵌的 `#[cfg(test)]` 模块包含对 scope 解析优先级和重试逻辑的完整单元测试
