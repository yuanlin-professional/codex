# `default_client.rs` — 默认 Codex HTTP 客户端

## 功能概述
构建带有标准 User-Agent、originator header 和可选 residency header 的 reqwest HTTP 客户端。User-Agent 包含版本号、操作系统信息和架构。支持自定义 originator（通过环境变量或 API）、sandbox 模式下禁用代理、以及自定义 CA 证书。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_client` | `fn() -> CodexHttpClient` | 创建带默认 header 的 HTTP 客户端 |
| `get_codex_user_agent` | `fn() -> String` | 生成 Codex User-Agent 字符串 |
| `set_default_originator` | `fn(value) -> Result<()>` | 设置全局 originator（仅可设置一次） |
| `originator` | `fn() -> Originator` | 获取当前 originator |

## 依赖关系
- **引用的 crate**: `codex_client`, `codex_terminal_detection`, `os_info`, `reqwest`
- **被引用方**: 所有需要 HTTP 请求的模块
