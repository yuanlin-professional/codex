# `upstream.rs` -- 上游 HTTP 客户端

## 功能概述
封装 rama 框架的 HTTP 客户端，提供直连、环境变量代理和 Unix 套接字三种连接模式。直连模式使用 TcpConnector + TLS，代理模式从 HTTP_PROXY/HTTPS_PROXY/ALL_PROXY 环境变量读取代理地址，Unix 套接字模式（仅 macOS）通过 UnixConnector 连接本地守护进程。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `UpstreamClient` | struct | 上游 HTTP 客户端，封装连接器和代理配置 |
| `ProxyConfig` | struct | 代理配置，分别存储 http/https/all 代理地址 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `UpstreamClient::direct` | `fn() -> Self` | 创建直连客户端 |
| `UpstreamClient::from_env_proxy` | `fn() -> Self` | 从环境变量创建代理客户端 |
| `UpstreamClient::unix_socket` | `fn(path) -> Self` | 创建 Unix 套接字客户端（仅 macOS） |
| `proxy_for_connect` | `fn() -> Option<ProxyAddress>` | 获取 CONNECT 隧道的上游代理地址 |

## 依赖关系
- **引用的 crate**: `rama_core`, `rama_http_backend`, `rama_tcp`, `rama_tls_rustls`, `rama_unix`
- **被引用方**: `http_proxy.rs`, `mitm.rs`

## 实现备注
- 仅支持 HTTP 协议的上游代理，忽略非 HTTP 协议的代理配置
