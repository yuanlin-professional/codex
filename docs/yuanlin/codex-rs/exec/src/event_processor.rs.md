# `event_processor.rs` — 事件处理器 trait 定义

## 功能概述

定义了 `EventProcessor` trait，是 codex-exec 输出层的核心抽象。该 trait 有两个实现：人类可读输出和 JSONL 输出。文件还包含 `CodexStatus` 枚举用于控制主循环的运行/关闭状态，以及辅助函数 `handle_last_message` 用于将最终消息写入指定文件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexStatus` | enum | `Running` 或 `InitiateShutdown`，控制事件循环生命周期 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `EventProcessor::print_config_summary` | `(&mut self, config, prompt, session_configured)` | 打印有效配置和用户提示摘要 |
| `EventProcessor::process_server_notification` | `(&mut self, notification) -> CodexStatus` | 处理来自 app-server 的单个通知 |
| `EventProcessor::process_warning` | `(&mut self, message) -> CodexStatus` | 处理本地 exec 级别的警告 |
| `EventProcessor::print_final_output` | `(&mut self)` | 输出最终结果（默认空实现） |
| `handle_last_message` | `(last_agent_message, output_file)` | 将最后一条代理消息写入文件 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`codex_core`、`codex_protocol`
- **被引用方**: `event_processor_with_human_output.rs`、`event_processor_with_jsonl_output.rs`、`lib.rs`
