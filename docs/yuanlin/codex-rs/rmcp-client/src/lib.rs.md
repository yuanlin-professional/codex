# `lib.rs` -- rmcp-client crate 入口

## 功能概述
rmcp-client crate 的库入口文件，声明并组织子模块，同时统一导出公共 API。导出的内容包括 OAuth 认证状态检测、OAuth 凭据管理、OAuth 登录流程、MCP 客户端核心类型以及 elicitation 相关类型。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RmcpClient` | re-export | MCP 客户端核心结构体 |
| `OAuthCredentialsStoreMode` | re-export | OAuth 凭据存储模式（Auto/File/Keyring） |
| `McpAuthStatus` | re-export | MCP 认证状态枚举 |

## 依赖关系
- **引用的 crate**: 内部模块 `auth_status`, `logging_client_handler`, `oauth`, `perform_oauth_login`, `program_resolver`, `rmcp_client`, `utils`
- **被引用方**: 整个 codex 项目中使用 MCP 客户端功能的模块
