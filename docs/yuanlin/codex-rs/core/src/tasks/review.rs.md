# `review.rs` — 代码审查任务

## 功能概述

实现了 `ReviewTask`，负责驱动代码审查（Review）工作流。它启动一个子代理（sub-agent）对话来执行代码审查，使用专用的 review 提示词和受限配置（禁用 web 搜索、协作工具等）。审查完成后，解析结构化的审查输出（findings + 总体说明），发射 `ExitedReviewMode` 事件，并将审查结果记录到对话历史中。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ReviewTask` | 结构体 | 代码审查任务，实现 `SessionTask` trait，`TaskKind` 为 `Review` |
| `REVIEW_EXIT_SUCCESS_TEMPLATE` | 静态常量 | Review 成功退出时的模板，使用 `LazyLock` 延迟解析 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn() -> Self` | 创建新的审查任务 |
| `run` | `async fn(self: Arc<Self>, session, ctx, input, cancellation_token) -> Option<String>` | 启动审查子代理对话 → 处理审查事件流 → 退出审查模式 |
| `abort` | `async fn(&self, session, ctx)` | 中止时以 `None` 退出审查模式 |
| `start_review_conversation` | `async fn(session, ctx, input, cancellation_token) -> Option<Receiver<Event>>` | 创建受限配置的子代理对话，返回事件接收器 |
| `process_review_events` | `async fn(session, ctx, receiver) -> Option<ReviewOutputEvent>` | 处理子代理事件流：转发非 assistant 消息事件，从最终 agent 消息中解析审查输出 |
| `parse_review_output_event` | `fn(text: &str) -> ReviewOutputEvent` | 解析审查输出：尝试 JSON 反序列化 → 提取 JSON 子串 → 回退为纯文本 |
| `exit_review_mode` | `pub(crate) async fn(session, review_output, ctx)` | 发射退出审查模式事件，记录用户/助手消息到对话历史 |
| `render_review_exit_success` | `fn(results: &str) -> String` | 使用模板渲染审查成功退出消息 |
| `normalize_review_template_line_endings` | `fn(template: &str) -> Cow<str>` | 将模板中的 CRLF 和 CR 规范化为 LF |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio_util`、`serde_json`、`codex_protocol`（事件和模型类型）、`codex_utils_template`、`codex_features`
- **引用的内部模块**: `crate::codex::Session`/`TurnContext`、`crate::codex_delegate::run_codex_thread_one_shot`、`crate::review_format`、`crate::config::Constrained`
- **被引用方**: `tasks/mod.rs`（re-export 为 `ReviewTask`）

## 实现备注

- 审查子代理的配置被严格限制：审批策略强制为 `Never`、禁用 web 搜索、禁用协作和 CSV 工具。
- 事件处理中故意隐藏 `AgentMessage`、`AgentMessageDelta`、`ItemCompleted`（助手消息类型）等事件，因为审查流使用结构化输出替代。
- JSON 解析采用三层回退策略：完整 JSON → 提取首尾大括号之间的子串 → 纯文本回退到 `overall_explanation` 字段。
- 文件内嵌了两个小型测试：验证模板渲染和换行符规范化。
