# `plan.rs` — 任务计划更新工具处理器

## 功能概述
该文件实现了 `update_plan` 工具的处理逻辑，允许模型以结构化方式记录和更新任务计划。模型可以提交包含步骤列表及其状态（pending、in_progress、completed）的计划，客户端可以读取并渲染这些计划。该工具本身不执行实际操作，其核心价值在于将计划数据暴露给客户端进行展示。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PlanHandler` | struct | 实现 `ToolHandler` trait 的计划处理器 |
| `PlanToolOutput` | struct | 计划工具的输出类型，实现 `ToolOutput` trait |
| `PLAN_TOOL` | static (LazyLock) | 工具规格定义，包含 JSON Schema 参数描述 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<PlanToolOutput, FunctionCallError>` | ToolHandler 的核心方法，解析 payload 并调用 `handle_update_plan` |
| `handle_update_plan` | `pub(crate) async fn handle_update_plan(session, turn_context, arguments, call_id) -> Result<String, FunctionCallError>` | 实际的计划更新逻辑：在 Plan 模式下拒绝调用，解析参数后发送 `PlanUpdate` 事件 |
| `parse_update_plan_arguments` | `fn parse_update_plan_arguments(arguments: &str) -> Result<UpdatePlanArgs, FunctionCallError>` | 将 JSON 字符串反序列化为 `UpdatePlanArgs` |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol`（`ModeKind`, `UpdatePlanArgs`, `EventMsg`）, `serde_json`
- **内部依赖**: `client_common::tools`, `codex::Session/TurnContext`, `function_tool::FunctionCallError`, `tools::context`, `tools::registry`, `tools::spec::JsonSchema`
- **被引用方**: 工具注册表中注册为 `update_plan` 工具

## 实现备注
- 工具的 JSON Schema 定义了 `explanation`（可选）和 `plan`（包含 step/status 的数组）参数
- 在 Plan 模式下禁止调用 `update_plan`，因为它是一个 TODO/清单工具
- 工具的输出对模型来说并不重要，关键在于输入数据能被客户端读取和展示
- `PLAN_TOOL` 使用 `LazyLock` 进行延迟初始化
