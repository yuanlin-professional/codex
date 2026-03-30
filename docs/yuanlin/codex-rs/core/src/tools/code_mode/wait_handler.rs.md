# `wait_handler.rs` — Code Mode 等待处理器

## 功能概述
实现 `CodeModeWaitHandler`，用于处理 Code Mode 中正在运行脚本的等待/终止操作。当 JavaScript 脚本通过 yield 暂停执行后，模型可以调用此工具等待脚本继续执行或主动终止它。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CodeModeWaitHandler` | struct | Code Mode 等待处理器，实现 `ToolHandler` trait |
| `ExecWaitArgs` | struct | 反序列化参数：`cell_id`、`yield_time_ms`、`max_tokens`、`terminate` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation) -> Result<Self::Output, FunctionCallError>` | 解析 JSON 参数，调用 `code_mode_service.wait()` 并处理响应 |
| `parse_arguments` | `fn parse_arguments<T>(arguments: &str) -> Result<T, FunctionCallError>` | 通用 JSON 参数解析辅助函数 |
| `default_wait_yield_time_ms` | `fn() -> u64` | 提供默认的 yield 等待时间 |

## 依赖关系
- **引用的 crate**: `async_trait`, `serde`
- **内部依赖**: `super::ExecContext`, `super::handle_runtime_response`, `super::WAIT_TOOL_NAME`
- **被引用方**: `code_mode/mod.rs` 导出供 `spec.rs` 注册

## 实现备注
- 只处理 `ToolPayload::Function` 类型的调用
- `terminate` 字段默认为 `false`，设为 `true` 时会终止正在运行的脚本
- `yield_time_ms` 有默认值 `DEFAULT_WAIT_YIELD_TIME_MS`，控制最大等待时间
