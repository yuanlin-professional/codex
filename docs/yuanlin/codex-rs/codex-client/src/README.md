# codex-client/src — 源代码目录

## 功能概述
codex-client crate 的源代码实现目录。Codex HTTP 客户端，处理 API 通信。

## 目录结构
| 文件 | 说明 |
|------|------|
| `custom_ca.rs` | 自定义 CA 支持 |
| `default_client.rs` | 默认客户端 |
| `error.rs` | 错误类型 |
| `lib.rs` | 库入口 |
| `request.rs` | 请求构建 |
| `retry.rs` | 重试逻辑 |
| `sse.rs` | SSE 流处理 |
| `telemetry.rs` | 遥测 |
| `transport.rs` | 传输层 |
