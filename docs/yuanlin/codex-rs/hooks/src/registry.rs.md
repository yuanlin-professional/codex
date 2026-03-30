# `registry.rs` -- Hooks 注册表与调度中心

## 功能概述

该文件实现了 Hooks 注册表（`Hooks` 结构体），是钩子系统的核心调度器。它负责根据配置初始化钩子列表（包括旧式通知钩子和 Claude Hooks Engine 驱动的新式钩子），将事件分发给匹配的钩子处理程序，并支持多种生命周期事件的预览和执行：session_start、pre_tool_use、post_tool_use、user_prompt_submit 和 stop。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HooksConfig` | struct | 钩子配置，包含 legacy_notify_argv、feature_enabled、config_layer_stack、shell 设置 |
| `Hooks` | struct | 钩子注册表，持有 after_agent 和 after_tool_use 钩子列表以及 ClaudeHooksEngine |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Hooks::new` | `fn(config: HooksConfig) -> Self` | 根据配置创建 Hooks 实例，初始化旧式通知和引擎 |
| `Hooks::dispatch` | `async fn(&self, hook_payload: HookPayload) -> Vec<HookResponse>` | 按事件类型分发钩子，顺序执行并支持中止 |
| `Hooks::run_session_start` | `async fn(&self, request, turn_id) -> SessionStartOutcome` | 执行 session_start 钩子 |
| `Hooks::run_pre_tool_use` | `async fn(&self, request) -> PreToolUseOutcome` | 执行 pre_tool_use 钩子 |
| `Hooks::run_post_tool_use` | `async fn(&self, request) -> PostToolUseOutcome` | 执行 post_tool_use 钩子 |
| `Hooks::run_user_prompt_submit` | `async fn(&self, request) -> UserPromptSubmitOutcome` | 执行 user_prompt_submit 钩子 |
| `Hooks::run_stop` | `async fn(&self, request) -> StopOutcome` | 执行 stop 钩子 |
| `Hooks::startup_warnings` | `fn(&self) -> &[String]` | 返回引擎启动时的警告信息 |
| `command_from_argv` | `fn(argv: &[String]) -> Option<Command>` | 从参数向量创建 tokio Command |

## 依赖关系

- **引用的 crate**: `codex_config`, `tokio`, `crate::engine`, `crate::events::*`, `crate::types`
- **被引用方**: 作为公共 API 被 `lib.rs` 重导出，被上层应用（TUI/CLI）使用

## 实现备注

- `dispatch` 方法顺序执行钩子，当某钩子返回 `FailedAbort` 时立即停止后续钩子
- 各 `preview_*` 方法返回 `HookRunSummary` 用于 UI 展示即将执行的钩子
- `command_from_argv` 检查第一个参数非空才创建命令
