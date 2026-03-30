# `socks5.rs` -- SOCKS5 代理服务器

## 功能概述
实现网络代理的 SOCKS5 TCP 和 UDP 代理服务器。与 HTTP 代理类似，每个连接/数据报经过完整的策略链评估：启用检查、Limited 模式阻断（SOCKS5 在 Limited 模式下完全不可用）、域名策略评估。支持 UDP 关联审计。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_socks5` | `async fn(state, addr, policy_decider, enable_udp) -> Result<()>` | 绑定地址并启动 SOCKS5 代理 |
| `run_socks5_with_std_listener` | `async fn(state, listener, ...) -> Result<()>` | 使用预绑定监听器启动 |
| `handle_socks5_tcp` | `async fn(req, connector, policy_decider) -> Result<...>` | 处理 SOCKS5 TCP 连接请求的策略评估 |
| `inspect_socks5_udp` | `async fn(request, state, policy_decider) -> io::Result<RelayResponse>` | 处理 SOCKS5 UDP 数据报的策略审计 |

## 依赖关系
- **引用的 crate**: `rama_socks5`, `rama_tcp`, `rama_core`
- **被引用方**: `proxy.rs` 启动 SOCKS5 代理任务

## 实现备注
- Limited 模式下 SOCKS5 TCP 和 UDP 都被完全阻断
- UDP 审计通过 `DefaultUdpRelay` 的 async inspector 实现
