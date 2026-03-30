# `js_repl.rs` -- JavaScript REPL 工具处理器

## 功能概述
该文件实现了 JavaScript REPL（交互式解释器）工具的处理器，允许模型在对话过程中执行 JavaScript 代码。它支持两种输入模式：标准 JSON 函数调用和 freeform 纯文本输入。freeform 模式下支持通过 `// codex-js-repl:` 前缀指令设置超时等参数。此外还提供了 `JsReplResetHandler` 用于重置 REPL 内核状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `JsReplHandler` | struct | 实现 `ToolHandler` trait，处理 `js_repl` 工具调用，执行 JavaScript 代码 |
| `JsReplResetHandler` | struct | 实现 `ToolHandler` trait，处理 `js_repl_reset` 工具调用，重置 REPL 内核 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `JsReplHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 检查 feature flag、解析参数、通过 REPL manager 执行代码、发送执行事件并返回输出 |
| `JsReplResetHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 检查 feature flag 并调用 REPL manager 的 reset 方法 |
| `parse_freeform_args` | `fn parse_freeform_args(input: &str) -> Result<JsReplArgs, FunctionCallError>` | 解析 freeform 文本输入：提取 `// codex-js-repl:` 前缀指令中的 `timeout_ms` 参数，剩余部分作为代码 |
| `reject_json_or_quoted_source` | `fn reject_json_or_quoted_source(code: &str) -> Result<(), FunctionCallError>` | 防御性检查：拒绝 JSON 对象、JSON 字符串或 markdown 代码块格式的输入 |
| `join_outputs` | `fn join_outputs(stdout: &str, stderr: &str) -> String` | 合并 stdout 和 stderr 输出 |
| `build_js_repl_exec_output` | `fn build_js_repl_exec_output(output, error, duration) -> ExecToolCallOutput` | 将 REPL 执行结果构造为标准的命令执行输出格式 |
| `emit_js_repl_exec_begin` | `async fn emit_js_repl_exec_begin(session, turn, call_id)` | 发送 REPL 执行开始事件 |
| `emit_js_repl_exec_end` | `async fn emit_js_repl_exec_end(session, turn, call_id, output, error, duration)` | 发送 REPL 执行结束事件，区分成功和失败 |

## 依赖关系
- **引用的 crate**: `codex_features`（Feature flag 检查）、`codex_protocol`（`FunctionCallOutputContentItem`）、`async_trait`、`serde_json`
- **被引用方**: 由工具注册表注册为 `js_repl` 和 `js_repl_reset` 工具的处理器

## 实现备注
- REPL 执行受 `Feature::JsRepl` feature flag 控制，未启用时直接返回错误。
- freeform 输入模式通过 `// codex-js-repl:` pragma 支持配置参数，目前仅支持 `timeout_ms`，采用 `key=value` 空格分隔格式。
- 对误用 freeform 工具的情况有多重防御：拒绝 JSON 包装的代码（`{"code":"..."}`）、拒绝 markdown 代码块（```...```）、拒绝 JSON 字符串字面量。
- 执行事件通过 `ToolEmitter::shell` 发送，伪装为 shell 命令执行事件以保持事件格式一致性。
- 输出结果支持多内容项（`content_items`），可包含文本和其他内容类型（如图像等）。
