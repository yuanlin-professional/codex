# `post_tool_use.rs` -- PostToolUse 钩子事件处理

## 功能概述

该文件实现了 `PostToolUse` 钩子事件的完整处理流程。当工具（如 Bash）执行完毕后触发该钩子，handler 可以：(1) 通过 `continue:false` 停止后续处理；(2) 通过 `decision:block` 提供反馈信息给模型；(3) 通过 `additionalContext` 向模型注入额外上下文；(4) 通过退出码 2 + stderr 提供反馈。支持停止原因和反馈消息的聚合。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PostToolUseRequest` | struct | 请求参数：session_id、turn_id、tool_name、command、tool_response 等 |
| `PostToolUseOutcome` | struct | 执行结果：hook_events、should_stop、stop_reason、additional_contexts、feedback_message |
| `PostToolUseHandlerData` | struct (内部) | 单个 handler 的解析数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `preview` | `fn(handlers, request) -> Vec<HookRunSummary>` | 预览将执行的 handler 列表 |
| `run` | `async fn(handlers, shell, request) -> PostToolUseOutcome` | 执行所有匹配的 handler 并聚合结果 |
| `parse_completed` | `fn(handler, run_result, turn_id) -> ParsedHandler<PostToolUseHandlerData>` | 解析单个 handler 的命令输出 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `serde_json`, `crate::engine`, `crate::schema`
- **被引用方**: `registry.rs` 中的 `Hooks::run_post_tool_use`

## 实现备注

- `continue:false` 时，stop_reason 取自 `stopReason` 字段，feedback 取自 `reason` 字段（回退到 stop 文本）
- `additionalContext` 仅在无 invalid_reason 和 invalid_block_reason 时生效
- 不支持 `updatedMCPToolOutput`（标记为 Failed）
- 退出码 2 的 stderr 反馈不标记为 Blocked（与 pre_tool_use 不同），而是标记为 Completed
- 纯文本 stdout（非 JSON）被静默忽略
- JSON 格式但不符合 schema 的输出标记为 Failed
- 包含 6 个测试覆盖 block/context/stop/退出码/纯文本等场景
