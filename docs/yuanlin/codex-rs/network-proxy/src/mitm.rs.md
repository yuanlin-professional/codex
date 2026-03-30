# `mitm.rs` -- MITM 中间人 TLS 拦截

## 功能概述
实现 HTTPS CONNECT 隧道的中间人（MITM）TLS 终止和策略执行。在 Limited 模式下，由于 CONNECT 隧道会隐藏内部 HTTP 请求，MITM 生成/加载本地 CA 并为每个目标主机签发叶证书，终止 TLS 后对内部请求执行方法策略（仅允许 GET/HEAD/OPTIONS）、主机匹配验证和本地/私有地址防 DNS 重绑定检查。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MitmState` | struct | MITM 状态，包含 CA、上游客户端、body 检查开关和最大 body 字节数 |
| `MitmPolicyContext` | struct | 策略上下文，包含目标主机、端口、网络模式和应用状态 |
| `MitmRequestContext` | struct | 请求上下文，组合策略上下文和 MITM 状态 |
| `InspectStream` | struct | 可选的 body 检查流，记录传输字节数和截断状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `MitmState::new` | `fn(allow_upstream_proxy) -> Result<Self>` | 创建 MITM 状态，加载/生成 CA |
| `mitm_tunnel` | `async fn(upgraded) -> Result<()>` | 在升级的 CONNECT 流上执行 MITM TLS 终止和请求代理 |
| `mitm_blocking_response` | `async fn(req, policy) -> Result<Option<Response>>` | 检查请求是否应被阻止（CONNECT/方法策略/主机不匹配/本地地址） |
| `forward_request` | `async fn(req, request_ctx) -> Result<Response>` | 转发内部 HTTPS 请求到上游 |

## 依赖关系
- **引用的 crate**: `rama_core`, `rama_http`, `rama_tls_rustls`, `rama_http_backend`
- **被引用方**: `http_proxy.rs` 在 Limited 模式 CONNECT 处理中使用

## 实现备注
- 默认不启用 body 检查（`MITM_INSPECT_BODIES = false`），最大检查 4096 字节
- body 检查通过流包装实现，在流结束时记录日志
