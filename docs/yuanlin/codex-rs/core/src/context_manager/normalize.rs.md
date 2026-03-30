# `normalize.rs` — 对话历史规范化工具

## 功能概述

`normalize.rs` 提供了一组用于规范化对话历史的工具函数，确保发送给模型的历史记录满足 API 约束。主要职责包括：为缺失输出的工具调用补充 "aborted" 占位输出、移除没有对应调用的孤立输出、在模型不支持图片时将图片内容替换为占位文本、以及在移除历史条目时维护调用/输出的配对关系。这些规范化操作保证了对话历史的结构完整性。

## 关键结构体/枚举

无独立结构体/枚举定义。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ensure_call_outputs_present` | `fn ensure_call_outputs_present(items: &mut Vec<ResponseItem>)` | 为所有缺失输出的调用（FunctionCall、CustomToolCall、LocalShellCall、ToolSearchCall）插入 "aborted" 合成输出 |
| `remove_orphan_outputs` | `fn remove_orphan_outputs(items: &mut Vec<ResponseItem>)` | 移除没有对应调用的 FunctionCallOutput、CustomToolCallOutput 和客户端 ToolSearchOutput |
| `remove_corresponding_for` | `fn remove_corresponding_for(items: &mut Vec<ResponseItem>, item: &ResponseItem)` | 给定一个被移除的条目，找到并移除其对应的配对条目（调用-输出双向查找） |
| `strip_images_when_unsupported` | `fn strip_images_when_unsupported(input_modalities, items: &mut [ResponseItem])` | 当模型不支持图片输入时，将所有图片内容替换为文本占位符 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ContentItem`, `FunctionCallOutputContentItem`, `FunctionCallOutputPayload`, `ResponseItem`, `InputModality`）
- **引用的内部模块**: `crate::util::error_or_panic`
- **被引用方**: `ContextManager::normalize_history`、`ContextManager::remove_first_item`、`ContextManager::remove_last_item`

## 实现备注

- `ensure_call_outputs_present` 收集所有需要插入的合成输出后，按逆序插入以避免索引偏移问题
- `remove_orphan_outputs` 分别收集四种调用类型的 call_id 集合，然后使用 `retain` 一次过滤
- 在 debug 构建中，缺失输出和孤立输出会通过 `error_or_panic` 触发 panic；在 release 构建中则静默修复
- `strip_images_when_unsupported` 处理三种包含图片的条目类型：`Message`、`FunctionCallOutput`/`CustomToolCallOutput` 以及 `ImageGenerationCall`（清空 result 字段）
- `remove_corresponding_for` 支持所有调用-输出配对关系，包括 `LocalShellCall` 到 `FunctionCallOutput` 的特殊映射
- 服务器端的 `ToolSearchOutput`（`execution == "server"`）不被视为孤立输出，即使没有匹配的调用也会被保留
