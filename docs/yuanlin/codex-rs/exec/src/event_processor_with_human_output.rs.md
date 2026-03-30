# `event_processor_with_human_output.rs` — 人类可读终端输出处理器

## 功能概述

实现了 `EventProcessor` trait 的终端友好输出版本。将 app-server 通知转换为带有 ANSI 颜色样式的 stderr 输出，适用于交互式终端场景。处理命令执行结果、MCP 工具调用、代理消息、推理摘要、补丁应用等所有事件类型的人类可读渲染。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EventProcessorWithHumanOutput` | struct | 持有 ANSI 样式配置和状态，包括最终消息、token 用量等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_with_ansi` | `(with_ansi, config, last_message_path) -> Self` | 根据是否启用 ANSI 创建处理器实例 |
| `render_item_started` | `(&self, item)` | 渲染事件项开始（命令、MCP 调用、Web 搜索等） |
| `render_item_completed` | `(&mut self, item)` | 渲染事件项完成，更新最终消息 |
| `print_config_summary` | `(&mut self, config, prompt, session_configured)` | 打印版本、配置摘要（workdir、model、sandbox 等） |
| `config_summary_entries` | `(config, session_configured) -> Vec<(&str, String)>` | 提取配置键值对 |
| `summarize_sandbox_policy` | `(sandbox_policy) -> String` | 将沙箱策略转为可读字符串 |
| `reasoning_text` | `(summary, content, show_raw) -> Option<String>` | 选择显示推理摘要还是原始推理内容 |
| `should_print_final_message_to_stdout` | `(msg, stdout_is_terminal, stderr_is_terminal) -> bool` | 仅当 stdout 或 stderr 非终端时输出到 stdout |
| `should_print_final_message_to_tty` | `(msg, rendered, stdout_tty, stderr_tty) -> bool` | 当消息未渲染且双流都是终端时额外输出到 stderr |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`codex_core`、`codex_protocol`、`owo_colors`
- **被引用方**: `exec/src/lib.rs`（作为默认输出处理器）

## 实现备注

- 当 `TurnCompleted` 到达时，若 turn.items 中有 `AgentMessage`，会更新 `final_message`；若 turn 失败或中断，则清除消息。
- `print_final_output` 根据 stdout/stderr 是否为终端决定将最终消息输出到 stdout（管道场景）还是 stderr（双终端场景）。
- token 用量显示的是"混合总量"，即非缓存输入 + 输出 token 数。
