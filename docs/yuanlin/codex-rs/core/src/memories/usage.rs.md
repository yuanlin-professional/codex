# `usage.rs` -- 记忆文件使用量指标追踪

## 功能概述

追踪工具调用中对记忆文件的读取使用情况，并上报 OpenTelemetry 指标。通过解析 shell/shell_command/exec_command 工具的参数，识别命令中涉及的记忆文件路径（MEMORY.md、memory_summary.md、raw_memories.md、rollout_summaries/、skills/），然后为每种记忆文件类型发出对应的计数指标。仅对已知安全命令进行路径分析。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MemoriesUsageKind` | 枚举 | 记忆文件类型：MemoryMd、MemorySummary、RawMemories、RolloutSummaries、Skills |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `emit_metric_for_tool_read` | `async fn(&ToolInvocation, bool)` | 根据工具调用内容上报记忆文件使用指标 |
| `memories_usage_kinds_from_invocation` | `async fn(&ToolInvocation) -> Vec<MemoriesUsageKind>` | 解析工具调用参数，识别涉及的记忆文件类型 |
| `shell_command_for_invocation` | `fn(&ToolInvocation) -> Option<(Vec<String>, PathBuf)>` | 从不同工具类型（shell/shell_command/exec_command）提取命令和工作目录 |
| `get_memory_kind` | `fn(String) -> Option<MemoriesUsageKind>` | 根据路径字符串判断记忆文件类型 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（命令解析相关类型）
- **被引用方**: 工具执行管线中的读操作指标收集

## 实现备注

- 仅对 `is_known_safe_command` 返回 true 的命令进行路径分析，避免对不安全命令做不必要的解析。
- 支持 `shell`、`shell_command` 和 `exec_command` 三种工具类型的参数解析。
- 路径匹配基于字符串包含检查（`contains`），简洁但可能产生误匹配（如路径中恰好包含 "memories/MEMORY.md" 字样）。
- 指标格式为 `codex.memories.usage`，带有 `kind`、`tool`、`success` 三个标签维度。
