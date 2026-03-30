# `parallel.rs` — 并行工具调用运行时

## 功能概述
提供 `ToolCallRuntime`，负责并行化和序列化工具调用的执行。使用 `RwLock` 实现读写锁模型：支持并行的工具获取读锁并发执行，不支持并行的工具获取写锁独占执行。同时处理工具调用的取消（abort）逻辑和错误到响应的映射。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolCallRuntime` | struct | 工具调用运行时：router、session、turn_context、tracker、parallel_execution 锁 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_tool_call` | `fn(self, call, cancellation_token) -> impl Future<Output = Result<ResponseInputItem, CodexErr>>` | 处理工具调用，Fatal 错误上传播，其他错误转为失败响应 |
| `handle_tool_call_with_source` | `fn(self, call, source, cancellation_token) -> impl Future<Output = Result<AnyToolResult, FunctionCallError>>` | 带来源的工具调用处理，使用 `tokio::select!` 实现取消，`AbortOnDropHandle` 确保 task 清理 |
| `failure_response` | `fn(call, err) -> ResponseInputItem` | 将错误映射为不同载荷类型的失败响应 |
| `aborted_response` | `fn(call, secs) -> AnyToolResult` | 生成中止响应 |

## 依赖关系
- **引用的 crate**: `tokio`, `tokio_util`, `tracing`
- **内部依赖**: `crate::tools::router::ToolRouter`, `crate::tools::registry::AnyToolResult`
- **被引用方**: `code_mode/mod.rs`, `crate::codex` 主循环中的工具调度

## 实现备注
- 并行控制使用 `Arc<RwLock<()>>`：并行工具获取 `.read()`，非并行工具获取 `.write()`
- 取消消息格式根据工具类型定制（shell 类工具显示 wall time）
- 使用 `AbortOnDropHandle` 确保 Future 被取消时 spawned task 也被中止
- OTel span 记录 `aborted` 状态
