# `mod.rs` — 工具模块入口

## 功能概述
工具系统的顶层模块入口，声明所有子模块并提供共享的执行输出格式化函数。定义了遥测预览的限制常量，实现了 `format_exec_output_for_model_structured`（JSON 结构化格式）和 `format_exec_output_for_model_freeform`（人类可读格式）两种输出格式化方式。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_exec_output_for_model_structured` | `fn(exec_output, truncation_policy) -> String` | JSON 结构化格式：包含 output + metadata（exit_code, duration_seconds） |
| `format_exec_output_for_model_freeform` | `fn(exec_output, truncation_policy) -> String` | 人类可读格式：Exit code + Wall time + Output |
| `format_exec_output_str` | `fn(exec_output, truncation_policy) -> String` | 截断并格式化原始输出文本 |

## 依赖关系
- **子模块**: code_mode, context, discoverable, events, handlers, js_repl, network_approval, orchestrator, parallel, registry, router, runtimes, sandboxing, spec
- **引用的 crate**: `codex_utils_output_truncation`, `serde`
- **被引用方**: 通过 `pub use router::ToolRouter` 导出核心路由器

## 实现备注
- 遥测预览常量：`TELEMETRY_PREVIEW_MAX_BYTES = 2KiB`、`TELEMETRY_PREVIEW_MAX_LINES = 64`
- 超时命令的输出会前置 "command timed out after N milliseconds" 消息
- 时间格式化精度为 0.1 秒
