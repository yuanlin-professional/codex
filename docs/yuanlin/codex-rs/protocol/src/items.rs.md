# `items.rs` — 会话 Turn 项目类型定义

## 功能概述

定义了 Codex 会话中一个"turn"（轮次）内可能出现的各种项目类型，包括用户消息、Agent 消息、Hook 提示、计划、推理、网页搜索、图片生成和上下文压缩等。每种项目类型都提供了从新结构到旧版事件格式的转换方法（`as_legacy_event`/`as_legacy_events`），以保持向后兼容。还实现了 Hook 提示消息的 XML 序列化与反序列化。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TurnItem` | 枚举 | Turn 中的所有项目类型的联合枚举 |
| `UserMessageItem` | 结构体 | 用户消息项，包含多种输入内容 |
| `AgentMessageItem` | 结构体 | Agent 回复消息项，可携带阶段和记忆引用 |
| `HookPromptItem` | 结构体 | Hook 提示项，包含多个片段 |
| `HookPromptFragment` | 结构体 | 单个 Hook 提示片段（文本 + hook_run_id） |
| `PlanItem` | 结构体 | 计划项 |
| `ReasoningItem` | 结构体 | 推理项（摘要文本 + 原始内容） |
| `WebSearchItem` | 结构体 | 网页搜索结果项 |
| `ImageGenerationItem` | 结构体 | 图片生成结果项 |
| `ContextCompactionItem` | 结构体 | 上下文压缩标记项 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `UserMessageItem::message` | `fn message(&self) -> String` | 拼接所有文本内容为单一字符串 |
| `UserMessageItem::text_elements` | `fn text_elements(&self) -> Vec<TextElement>` | 提取并重建偏移后的文本元素 |
| `build_hook_prompt_message` | `fn build_hook_prompt_message(fragments: &[HookPromptFragment]) -> Option<ResponseItem>` | 将 Hook 片段序列化为 ResponseItem |
| `parse_hook_prompt_message` | `fn parse_hook_prompt_message(id, content) -> Option<HookPromptItem>` | 从 ResponseItem 内容反序列化 Hook 片段 |
| `TurnItem::as_legacy_events` | `fn as_legacy_events(&self, ...) -> Vec<EventMsg>` | 将新版项目转换为旧版事件列表 |

## 依赖关系

- **引用的 crate**: `quick_xml`, `schemars`, `serde`, `ts_rs`, `uuid`
- **被引用方**: `protocol.rs` 中的事件流，`app-server-protocol` 的 thread_history 模块

## 实现备注

- Hook 提示使用 XML 格式 `<hook_prompt hook_run_id="...">text</hook_prompt>` 序列化/反序列化，便于在现有文本流中嵌入结构化 Hook 标识。
- `UserMessageItem` 的 `text_elements` 方法会将各文本段的字节范围重新映射到拼接后的整体消息偏移上。
- 文件共 449 行，包含用户消息、Agent 消息、Hook 提示等多种 Turn 项目的完整生命周期处理。
