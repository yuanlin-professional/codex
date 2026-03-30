# `history.rs` — 对话历史上下文管理器

## 功能概述

`history.rs` 实现了 `ContextManager` 结构体，作为对话历史的核心管理组件。它负责记录和维护会话中所有的响应条目（包括消息、工具调用、工具输出、推理等），提供历史记录的截断处理、规范化、token 用量估算、图片内容替换、轮次回滚等功能。该模块还实现了精确的 token 使用量追踪机制，包括对 base64 编码的内联图片进行固定成本估算而非按原始字节计数，以更准确地反映模型实际消耗的 token 数量。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ContextManager` | struct | 对话历史转录管理器，持有按时间排序的响应条目列表、token 信息和引用上下文 |
| `TotalTokenUsageBreakdown` | struct | Token 使用量明细，包括最近 API 响应总 token、所有历史条目可见字节、新增条目的估算 token 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new() -> Self` | 创建空的上下文管理器 |
| `record_items` | `fn record_items<I>(&mut self, items, policy)` | 记录响应条目，对工具输出应用截断策略 |
| `for_prompt` | `fn for_prompt(self, input_modalities) -> Vec<ResponseItem>` | 准备发送给模型的历史：规范化并移除 GhostSnapshot |
| `estimate_token_count` | `fn estimate_token_count(&self, turn_context) -> Option<i64>` | 基于字节启发式估算当前历史的 token 数 |
| `remove_first_item` / `remove_last_item` | `fn remove_first_item(&mut self)` / `fn remove_last_item(&mut self) -> bool` | 移除首/末条目，同时移除对应的调用/输出配对项 |
| `replace` | `fn replace(&mut self, items: Vec<ResponseItem>)` | 替换全部历史条目 |
| `replace_last_turn_images` | `fn replace_last_turn_images(&mut self, placeholder) -> bool` | 将最后一轮工具输出中的图片替换为占位文本 |
| `drop_last_n_user_turns` | `fn drop_last_n_user_turns(&mut self, num_turns: u32)` | 回滚最后 N 个用户轮次 |
| `get_total_token_usage` | `fn get_total_token_usage(&self, server_reasoning_included) -> i64` | 获取总 token 使用量 |
| `normalize_history` | `fn normalize_history(&mut self, input_modalities)` | 规范化历史：确保调用/输出配对完整、移除孤立输出、剥离不支持的图片 |
| `estimate_response_item_model_visible_bytes` | `fn estimate_response_item_model_visible_bytes(item) -> i64` | 估算单个响应条目的模型可见字节数 |
| `is_user_turn_boundary` | `fn is_user_turn_boundary(item) -> bool` | 判断条目是否为用户轮次边界 |
| `is_codex_generated_item` | `fn is_codex_generated_item(item) -> bool` | 判断条目是否由 Codex 生成（工具输出或 developer 消息） |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_utils_output_truncation`, `codex_utils_cache`, `base64`, `image`
- **引用的内部模块**: `normalize`（历史规范化工具）、`TurnContext`、`EnvironmentContext`、事件映射中的上下文判断函数
- **被引用方**: `SessionState` 通过 `ContextManager` 管理对话历史；`context_manager/mod.rs` 导出其公共接口

## 实现备注

- 历史条目按从旧到新排序（最早的在向量前端）
- 截断策略使用 1.2x 的序列化预算来预留 JSON 包装开销
- 图片 token 估算采用固定成本模型：普通图片统一使用 `RESIZED_IMAGE_BYTES_ESTIMATE`（7373 字节，约 1844 token），`detail: "original"` 的图片根据实际像素尺寸按 32px patch 计算
- 使用 `LazyLock` + `BlockingLruCache`（容量 32）缓存 `original` 图片的估算结果，避免重复解码
- 加密推理内容（`encrypted_content`）的长度估算公式：`encoded_len * 3/4 - 650`
- 轮次回滚（`drop_last_n_user_turns`）支持 inter-agent 通信消息作为指令轮次边界
- `trim_pre_turn_context_updates` 在回滚时会清理轮次前注入的上下文更新消息，如果涉及混合的初始上下文包，则清除 `reference_context_item` 以强制下次完整重注入
- `is_api_message` 过滤掉 `system` 角色消息和 `GhostSnapshot`/`Other` 类型
