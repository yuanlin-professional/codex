# `auth_status.rs` -- Streamable HTTP MCP 服务器认证状态检测

## 功能概述
检测 Streamable HTTP MCP 服务器的认证状态，按优先级判断：Bearer 令牌环境变量 > Authorization 头 > 已存储的 OAuth 令牌 > OAuth 发现端点。实现了 RFC 8414 的 OAuth 授权服务器元数据发现，支持多候选路径探测。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `StreamableHttpOAuthDiscovery` | struct | OAuth 发现结果，包含 scopes_supported |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `determine_streamable_http_auth_status` | `async fn(...) -> Result<McpAuthStatus>` | 综合判断认证状态 |
| `supports_oauth_login` | `async fn(url) -> Result<bool>` | 检测服务器是否支持 OAuth 登录 |
| `discover_streamable_http_oauth` | `async fn(url, ...) -> Result<Option<StreamableHttpOAuthDiscovery>>` | 执行 OAuth 发现 |
| `discovery_paths` | `fn(base_path) -> Vec<String>` | 按 RFC 8414 生成 well-known 发现路径候选列表 |

## 依赖关系
- **引用的 crate**: `reqwest`, `codex_protocol`
- **被引用方**: `lib.rs` 公开导出

## 实现备注
- OAuth 发现超时 5 秒，使用 `no_proxy` 避免 system-configuration crate 可能的 panic
- scope 归一化：去重、去空白、过滤空字符串
