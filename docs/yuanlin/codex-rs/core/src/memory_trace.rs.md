# `memory_trace.rs` — 记忆追踪文件加载与摘要构建

## 功能概述

此文件负责加载原始追踪文件（trace files）、规范化其中的对话条目，并通过模型 API 构建记忆摘要。追踪文件可以是 JSON 数组或 JSONL 格式，支持带有 `payload` 字段的事件包装格式和裸 ResponseItem 格式。加载后的追踪条目经过类型过滤和规范化处理，最终通过 `/v1/memories/trace_summarize` API 端点生成记忆摘要。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `BuiltMemory` | 结构体 | 构建完成的记忆，包含 memory_id、来源路径、原始记忆和摘要 |
| `PreparedTrace` | 结构体（私有） | 准备好的追踪数据，包含 ID、来源路径和 API 载荷 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_memories_from_trace_files` | `async (client, trace_paths, model_info, effort, telemetry) -> Result<Vec<BuiltMemory>>` | 批量从追踪文件构建记忆摘要 |
| `prepare_trace` | `async (index, path) -> Result<PreparedTrace>` | 加载并准备单个追踪文件 |
| `load_trace_items` | `(path, text) -> Result<Vec<Value>>` | 解析追踪文本为 JSON 条目，支持数组和行格式 |
| `normalize_trace_items` | `(items, path) -> Result<Vec<Value>>` | 规范化追踪条目：展开事件包装、过滤无效类型 |
| `is_allowed_trace_item` | `(item) -> bool` | 判断追踪条目是否为允许的类型（message 需要有效角色） |
| `decode_trace_bytes` | `(raw: &[u8]) -> String` | 解码追踪文件字节，支持 UTF-8 BOM 和非 UTF-8 回退 |

## 依赖关系

- **引用的 crate**: `codex_api`（`RawMemory`、`RawMemoryMetadata`）、`codex_otel`、`codex_protocol`、`serde_json`
- **被引用方**: `lib.rs` 公开导出此模块，记忆系统在会话初始化时使用

## 实现备注

- 追踪文件格式自动检测：先尝试 JSON 数组，失败后逐行解析 JSONL
- 带 `payload` 字段的条目仅保留 `type == "response_item"` 的项
- 消息类型仅保留 assistant、system、developer、user 角色
- memory_id 格式为 `memory_{index}_{filename_stem}`
- UTF-8 BOM（0xEF, 0xBB, 0xBF）会被自动剥离
- API 输出数量必须与输入数量匹配，否则返回错误
