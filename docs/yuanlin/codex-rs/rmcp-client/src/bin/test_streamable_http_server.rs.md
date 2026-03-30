# `test_streamable_http_server.rs` -- Streamable HTTP 测试 MCP 服务器

## 功能概述
基于 axum 实现的 Streamable HTTP MCP 测试服务器，用于集成测试。提供 echo 工具调用、资源列表/读取，以及 OAuth 发现元数据端点。支持可选的 Bearer 令牌认证，并提供控制端点用于注入会话级 POST 失败（模拟 404/401/500 等错误），以测试客户端的会话恢复逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TestToolServer` | struct | MCP 服务端处理器，包含工具列表、资源和资源模板 |
| `SessionFailureState` | struct | 会话失败注入状态，用于测试控制 |
| `ArmedFailure` | struct | 已激活的失败配置，包含 HTTP 状态码和剩余次数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `async fn() -> Result<()>` | 启动 HTTP 服务器，绑定地址可通过环境变量配置 |
| `arm_session_post_failure` | `async fn(state, request) -> StatusCode` | 控制端点：激活/取消会话 POST 失败注入 |
| `fail_session_post_when_armed` | `async fn(state, request, next) -> Response` | 中间件：在目标 POST 请求上注入失败响应 |
| `require_bearer` | `async fn(expected, request, next) -> Result<Response>` | 中间件：验证 Bearer 令牌 |

## 依赖关系
- **引用的 crate**: `axum`, `rmcp`, `serde_json`, `tokio`
- **被引用方**: `streamable_http_recovery.rs` 集成测试

## 实现备注
- 绑定地址通过 `MCP_STREAMABLE_HTTP_BIND_ADDR` 或 `BIND_ADDR` 环境变量配置
- 支持绑定重试（最多 20 次，间隔 50ms）以处理端口占用
