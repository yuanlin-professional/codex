# `utils.rs` -- MCP 客户端工具函数

## 功能概述
提供 MCP 服务器子进程的环境变量构建和 HTTP 默认头部管理功能。环境变量白名单按平台区分（Unix 包含 HOME/PATH/SHELL 等，Windows 包含 PATH/PATHEXT/SYSTEMROOT 等），支持额外变量注入。HTTP 头部支持静态配置和环境变量引用两种方式。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_env_for_mcp_server` | `fn(extra_env, env_vars) -> HashMap<OsString, OsString>` | 构建 MCP 子进程环境变量，合并白名单变量和额外变量 |
| `build_default_headers` | `fn(http_headers, env_http_headers) -> Result<HeaderMap>` | 从静态配置和环境变量构建 HTTP 默认头部 |
| `apply_default_headers` | `fn(builder, default_headers) -> ClientBuilder` | 将默认头部应用到 reqwest ClientBuilder |

## 依赖关系
- **引用的 crate**: `reqwest`
- **被引用方**: `rmcp_client.rs`, `perform_oauth_login.rs`, `auth_status.rs`

## 实现备注
- Unix 白名单包含 11 个变量，Windows 白名单包含 16 个变量
- 环境变量引用头部（env_http_headers）值为空时会被跳过
