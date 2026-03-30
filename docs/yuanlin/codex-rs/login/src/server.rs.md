# `server.rs` — 本地 OAuth 回调服务器

## 功能概述
实现 CLI 交互式登录所用的本地 HTTP 回调服务器。处理 OAuth 授权码回调、PKCE 验证、token 交换、workspace 限制验证和凭据持久化。支持取消信号、端口冲突自动重试、错误页面渲染和敏感 URL 参数脱敏。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ServerOptions` | 结构体 | 服务器配置：codex_home, client_id, issuer, port, 浏览器打开, workspace 限制 |
| `LoginServer` | 结构体 | 运行中的登录服务器：auth_url, 端口, 任务句柄, 关闭句柄 |
| `ShutdownHandle` | 结构体 | 可克隆的关闭信号句柄 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_login_server` | `fn(opts) -> io::Result<LoginServer>` | 启动登录服务器并返回浏览器 auth URL |
| `exchange_code_for_tokens` | `async fn(issuer, ...) -> io::Result<ExchangedTokens>` | 授权码换 token |
| `ensure_workspace_allowed` | `fn(expected, id_token) -> Result<(), String>` | 验证 workspace 限制 |

## 依赖关系
- **引用的 crate**: `tiny_http`, `reqwest`, `base64`, `rand`, `codex_utils_template`
- **被引用方**: CLI 登录命令、device_code_auth

## 实现备注
使用 `send_response_with_disconnect` 绕过 tiny_http 的 Connection header 过滤问题。错误页面使用 HTML 模板渲染。含 10+ 单元测试验证错误解析和 URL 脱敏逻辑。
