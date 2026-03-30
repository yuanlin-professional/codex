# exec/src — 源代码目录

## 功能概述
exec crate 的源代码实现目录。Codex 非交互式执行模式（codex exec）。

## 目录结构
| 文件 | 说明 |
|------|------|
| `cli.rs` | 命令行参数 |
| `event_processor_with_human_output.rs` | 人类可读输出处理 |
| `event_processor_with_jsonl_output.rs` | JSONL 输出处理 |
| `event_processor.rs` | 事件处理器 |
| `exec_events.rs` | 执行事件 |
| `lib.rs` | 库入口 |
| `main.rs` | 程序入口 |
