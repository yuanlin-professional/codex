# network-proxy/src — 源代码目录

## 功能概述
network-proxy crate 的源代码实现目录。实现网络代理功能，包括 HTTP 和 SOCKS5 代理。

## 目录结构
| 文件 | 说明 |
|------|------|
| `certs.rs` | 证书处理 |
| `config.rs` | 代理配置 |
| `http_proxy.rs` | HTTP 代理实现 |
| `lib.rs` | 库入口 |
| `mitm_tests.rs` | 中间人测试 |
| `mitm.rs` | 中间人代理实现 |
| `network_policy.rs` | 网络策略 |
| `policy.rs` | 策略定义 |
| `proxy.rs` | 代理核心 |
| `reasons.rs` | 拒绝原因 |
| `responses.rs` | 响应处理 |
| `runtime.rs` | 运行时 |
| `socks5.rs` | SOCKS5 代理 |
| `state.rs` | 状态管理 |
| `upstream.rs` | 上游连接 |
