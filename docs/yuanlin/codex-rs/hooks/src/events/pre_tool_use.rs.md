# `pre_tool_use.rs` -- PreToolUse 钩子事件处理

## 功能概述

该文件实现了 `PreToolUse` 钩子事件的完整处理流程。当工具（如 Bash）即将被调用时，系统选择匹配的 handler，将请求序列化为 JSON 输入传递给外部命令，解析命令输出以确定是否应阻止工具执行。支持两种阻止方式：通过 JSON 输出中的 `decision:block` 或 `permissionDecision:deny`，以及通过退出码 2（stderr 中包含阻止原因）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PreToolUseRequest` | struct | 请求参数：session_id、turn_id、cwd、model、tool_name、command 等 |
| `PreToolUseOutcome` | struct | 执行结果：hook_events、should_block、block_reason |
| `PreToolUseHandlerData` | struct (内部) | 单个 handler 的解析结果数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `preview` | `fn(handlers, request) -> Vec<HookRunSummary>` | 预览将执行的 handler 列表 |
| `run` | `async fn(handlers, shell, request) -> PreToolUseOutcome` | 执行所有匹配的 handler 并聚合结果 |
| `parse_completed` | `fn(handler, run_result, turn_id) -> ParsedHandler<PreToolUseHandlerData>` | 解析单个 handler 的命令输出 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `crate::engine`, `crate::schema`
- **被引用方**: `registry.rs` 中的 `Hooks::run_pre_tool_use`

## 实现备注

- 退出码 0 + JSON 输出：解析 decision/permissionDecision 字段
- 退出码 2 + stderr：直接阻止，stderr 内容作为原因
- 其他退出码或无退出码：标记为 Failed
- 不支持 `permissionDecision:ask`（标记为 Failed）
- 不支持 `decision:approve`（标记为 Failed）
- 不支持 `additionalContext`（标记为 Failed）
- 纯文本 stdout 被静默忽略（非 JSON）
- 包含 8 个测试覆盖各种决策和退出码场景
