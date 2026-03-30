# `otlp.rs` — OTLP 传输层辅助工具

## 功能概述
该文件提供 OTLP 导出器所需的传输层辅助功能：HTTP Header 构建、gRPC TLS 配置、阻塞/异步 HTTP 客户端构建（含 mTLS 支持）、超时解析等。特别处理了在不同 tokio 运行时（多线程/单线程/无运行时）中构建阻塞 HTTP 客户端的兼容性问题。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 纯辅助函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_header_map` | `(headers: &HashMap) -> HeaderMap` | 从字符串映射构建 reqwest HeaderMap |
| `build_grpc_tls_config` | `(endpoint, tls_config, tls) -> Result<ClientTlsConfig>` | 构建 gRPC TLS 配置（CA + mTLS） |
| `build_http_client` | `(tls, timeout_var) -> Result<reqwest::blocking::Client>` | 构建阻塞 HTTP 客户端，兼容各种 tokio 运行时 |
| `build_async_http_client` | `(tls, timeout_var) -> Result<reqwest::Client>` | 构建异步 HTTP 客户端 |
| `current_tokio_runtime_is_multi_thread` | `() -> bool` | 检测当前是否在多线程 tokio 运行时中 |

## 依赖关系
- **引用的 crate**: `reqwest`, `opentelemetry_otlp`, `http`
- **被引用方**: `provider.rs`, `metrics/client.rs`

## 实现备注
- 在单线程 tokio 运行时中通过新线程构建阻塞客户端避免死锁
- OTLP 超时按信号变量 -> 通用变量 -> 默认值的顺序解析
