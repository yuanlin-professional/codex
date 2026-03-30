# `stop.rs` -- Stop 钩子事件处理

## 功能概述

该文件实现了 `Stop` 钩子事件的完整处理流程。当代理即将停止时触发该钩子，handler 可以通过输出决定：(1) 通过 `continue:false` 确认停止；(2) 通过 `decision:block` 加 `reason` 阻止停止并提供续行提示（continuation prompt）；(3) 通过退出码 2 + stderr 提供续行提示。多个 handler 的结果会被聚合：stop 优先于 block，block reason 会被连接。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StopRequest` | struct | 请求参数：session_id、turn_id、cwd、model、stop_hook_active、last_assistant_message 等 |
| `StopOutcome` | struct | 执行结果：hook_events、should_stop、stop_reason、should_block、block_reason、continuation_fragments |
| `StopHandlerData` | struct (内部) | 单个 handler 的解析结果数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `preview` | `fn(handlers, request) -> Vec<HookRunSummary>` | 预览将执行的 handler 列表 |
| `run` | `async fn(handlers, shell, request) -> StopOutcome` | 执行所有匹配的 handler 并聚合结果 |
| `parse_completed` | `fn(handler, run_result, turn_id) -> ParsedHandler<StopHandlerData>` | 解析单个 handler 的命令输出 |
| `aggregate_results` | `fn(results) -> StopHandlerData` | 聚合多个 handler 结果：stop 优先于 block |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`HookPromptFragment`）, `crate::engine`, `crate::schema`
- **被引用方**: `registry.rs` 中的 `Hooks::run_stop`

## 实现备注

- `continue:false` 设置 `should_stop=true`，优先于 block 决策
- `decision:block` 必须提供非空 `reason`，否则标记为 Failed
- 退出码 2 使用 stderr 内容作为 continuation prompt
- `aggregate_results` 中 `should_stop` 一旦为 true，`should_block` 将被忽略
- block reason 来自多个 handler 时以双换行连接
- continuation_fragments 通过 `HookPromptFragment::from_single_hook` 创建，关联 hook_run_id
- 包含 9 个测试覆盖 block/stop/aggregate/退出码等场景
