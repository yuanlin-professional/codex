# `http_proxy.rs` -- HTTP/HTTPS 代理服务器

## 功能概述
实现网络代理的 HTTP/1 代理服务器，处理普通 HTTP 请求（absolute-form）和 HTTPS CONNECT 隧道。每个请求经过完整的策略链：启用检查 -> 域名策略评估（允许/拒绝列表 + 外部决策器）-> 方法策略（Limited 模式仅允许 GET/HEAD/OPTIONS）-> MITM 或直连隧道。还支持 `x-unix-socket` 头部的本地 Unix 套接字代理（仅 macOS）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BlockedResponse` | struct | JSON 格式的阻断响应体 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_http_proxy` | `async fn(state, addr, policy_decider) -> Result<()>` | 绑定地址并启动 HTTP 代理 |
| `run_http_proxy_with_std_listener` | `async fn(state, listener, policy_decider) -> Result<()>` | 使用预绑定的 std 监听器启动 |
| `http_connect_accept` | `async fn(policy_decider, req) -> Result<(Response, Request), Response>` | 处理 CONNECT 请求的策略评估 |
| `http_connect_proxy` | `async fn(upgraded) -> Result<(), Infallible>` | 执行 CONNECT 隧道转发或 MITM 拦截 |
| `http_plain_proxy` | `async fn(policy_decider, req) -> Result<Response, Infallible>` | 处理普通 HTTP 代理请求 |

## 依赖关系
- **引用的 crate**: `rama_core`, `rama_http`, `rama_http_backend`, `rama_tcp`, `rama_tls_rustls`
- **被引用方**: `proxy.rs` 启动 HTTP 代理任务

## 实现备注
- Host 头部与 URI authority 的一致性验证防止请求走私
- hop-by-hop 头部（Connection、Proxy-Authorization 等）在转发前移除
- JSON 阻断响应包含 `x-proxy-error` 头部用于客户端识别阻断类型
