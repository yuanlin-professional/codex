# `rmcp_test_server.rs` -- 简化版 Stdio MCP 测试服务器

## 功能概述
一个简化的 stdio MCP 测试服务器，仅提供单个 echo 工具。echo 工具接收 message 参数并直接返回（不加 "ECHOING:" 前缀），同时返回 `MCP_TEST_VALUE` 环境变量值。用于基础集成测试场景。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TestToolServer` | struct | 仅包含 echo 工具的最小化 MCP 服务器 |
| `EchoArgs` | struct | echo 工具参数：message（必须）、env_var（可选） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `async fn() -> Result<()>` | 以 stdio 传输模式启动最小化 MCP 服务器 |

## 依赖关系
- **引用的 crate**: `rmcp`, `serde_json`, `tokio`
- **被引用方**: 集成测试使用

## 实现备注
- 与 `test_stdio_server.rs` 的区别在于 echo 返回原始消息而非加前缀版本，且不支持 tool annotations
