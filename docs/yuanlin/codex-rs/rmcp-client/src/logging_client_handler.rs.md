# `logging_client_handler.rs` -- MCP 客户端日志处理器

## 功能概述
实现 rmcp SDK 的 `ClientHandler` trait，将 MCP 服务器发送的各类通知（取消、进度、资源更新、列表变更、日志消息）按级别映射到 tracing 日志系统，同时处理 elicitation 请求的转发。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `LoggingClientHandler` | struct | 日志客户端处理器，包含客户端信息和 elicitation 发送回调 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(client_info, send_elicitation) -> Self` | 创建处理器实例 |
| `create_elicitation` | `async fn(&self, request, context) -> Result<CreateElicitationResult>` | 转发 elicitation 请求到 UI 层 |
| `on_logging_message` | `async fn(&self, params, context)` | 将 MCP 日志按级别映射到 error/warn/info/debug |

## 依赖关系
- **引用的 crate**: `rmcp`, `tracing`
- **被引用方**: `rmcp_client.rs` 在初始化时创建

## 实现备注
- Emergency/Alert/Critical/Error 级别映射为 tracing error
- Warning 映射为 warn，Notice/Info 映射为 info，Debug 映射为 debug
