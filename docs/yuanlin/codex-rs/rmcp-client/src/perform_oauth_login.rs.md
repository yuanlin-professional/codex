# `perform_oauth_login.rs` -- OAuth 登录流程实现

## 功能概述
实现了 MCP 服务器的完整 OAuth 登录流程，包括启动本地 HTTP 回调服务器、构建授权 URL、等待 OAuth 回调、交换授权码获取令牌并持久化保存。支持浏览器自动打开、静默模式和返回 URL 模式三种登录方式。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `OAuthProviderError` | struct | OAuth 提供者返回的错误，包含 error 和 error_description |
| `OauthLoginHandle` | struct | 包含授权 URL 和完成通知通道的登录句柄 |
| `OauthLoginFlow` | struct | OAuth 登录流程的内部状态机，管理回调服务器和 OAuth 状态 |
| `CallbackOutcome` | enum | 回调解析结果：成功、提供者错误或无效请求 |
| `CallbackServerGuard` | struct | 确保回调服务器在 drop 时被 unblock |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `perform_oauth_login` | `async fn(server_name, server_url, ...) -> Result<()>` | 执行标准 OAuth 登录，自动打开浏览器并输出授权 URL |
| `perform_oauth_login_silent` | `async fn(server_name, server_url, ...) -> Result<()>` | 静默模式登录，不输出浏览器 URL |
| `perform_oauth_login_return_url` | `async fn(...) -> Result<OauthLoginHandle>` | 返回授权 URL 和完成通道，不自动打开浏览器 |
| `parse_oauth_callback` | `fn(path, expected_callback_path) -> CallbackOutcome` | 解析 OAuth 回调 URL，提取 code 和 state 参数 |
| `append_query_param` | `fn(url, key, value) -> String` | 向 URL 追加查询参数 |

## 依赖关系
- **引用的 crate**: `anyhow`, `reqwest`, `rmcp`, `tiny_http`, `tokio`, `urlencoding`, `webbrowser`
- **被引用方**: `lib.rs` 公开导出登录函数

## 实现备注
- 回调服务器使用 `tiny_http` 运行在阻塞线程中，通过 `oneshot` 通道将回调结果发送回 async 上下文
- 默认 OAuth 超时为 300 秒，支持自定义回调端口和回调 URL
- 包含单元测试验证回调解析、URL 参数追加等边界情况
