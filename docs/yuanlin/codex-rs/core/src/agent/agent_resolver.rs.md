# `agent_resolver.rs` — Agent 目标引用解析器

## 功能概述

该模块负责将工具层面的 agent 目标引用（字符串形式）解析为具体的 `ThreadId`。它提供了单目标和批量目标两种解析方式，是多 agent 工具调用时定位目标线程的关键桥梁。在解析前会自动将当前 session 注册为根线程，以确保 agent 路径树的完整性。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_agent_target` | `async fn(session, turn, target: &str) -> Result<ThreadId, FunctionCallError>` | 将单个字符串目标解析为 `ThreadId`；优先尝试直接解析为 `ThreadId`，失败则通过 `AgentControl` 查找 agent 引用 |
| `resolve_agent_targets` | `async fn(session, turn, targets: Vec<String>) -> Result<Vec<ThreadId>, FunctionCallError>` | 批量解析多个目标引用，要求 targets 非空，依次调用 `resolve_agent_target` |
| `register_session_root` | `fn(session, turn)` | 内部辅助函数，确保当前 session 的根线程已注册到 `AgentControl` |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ThreadId`）
- **引用的内部模块**: `crate::codex::Session`、`crate::codex::TurnContext`、`crate::function_tool::FunctionCallError`
- **被引用方**: 多 agent 工具函数调用层（如 `spawn_agent`、`send_message` 等工具的参数解析）

## 实现备注

- 错误映射策略：将 `CodexErr::UnsupportedOperation` 转换为 `FunctionCallError::RespondToModel`，让模型能获得可理解的错误信息。
- 批量解析时采用顺序遍历而非并发，确保解析的确定性和错误传播的清晰性。
