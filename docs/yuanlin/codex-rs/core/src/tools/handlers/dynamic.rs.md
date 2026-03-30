# `dynamic.rs` -- 动态工具调用处理器

## 功能概述
该文件实现了动态工具（Dynamic Tool）的处理器。动态工具是指在运行时由外部客户端定义和处理的工具，Codex 仅负责将工具调用请求转发给客户端，等待客户端处理完成后将结果返回给模型。这种机制允许第三方集成自定义工具而无需修改 Codex 核心代码。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `DynamicToolHandler` | struct | 实现 `ToolHandler` trait，作为所有动态工具调用的统一处理器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `DynamicToolHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 解析参数、发送动态工具调用请求、等待响应并转换为工具输出 |
| `request_dynamic_tool` | `async fn request_dynamic_tool(session, turn_context, call_id, tool, arguments) -> Option<DynamicToolResponse>` | 通过 oneshot channel 注册待处理的动态工具调用，发送请求事件，等待客户端响应，并发送响应事件 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（`DynamicToolCallRequest`、`DynamicToolResponse`、`EventMsg` 等协议类型）、`tokio`（oneshot channel）、`serde_json`、`async_trait`、`tracing`
- **被引用方**: 由工具注册表在遇到动态注册工具时分发调用

## 实现备注
- 使用 `oneshot::channel` 实现请求-响应模式：处理器注册一个 channel 发送端到 `turn_state` 的 pending 映射中，然后发送 `DynamicToolCallRequest` 事件通知客户端，最后在接收端等待响应。
- 如果同一 `call_id` 已有 pending 的动态工具调用，会覆盖旧的并记录警告日志。
- 无论客户端响应成功或取消，都会发送 `DynamicToolCallResponse` 事件，确保事件流的完整性。
- 响应事件包含耗时信息（`duration`），通过 `Instant::now()` 在请求前后计算。
- `is_mutating` 返回 `true`，因为动态工具的行为无法预知，保守地将其标记为可变操作。
