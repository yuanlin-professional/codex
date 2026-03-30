# `event_mapping.rs` — 响应项到回合项的事件映射

## 功能概述

此文件负责将模型 API 返回的 `ResponseItem` 解析和映射为面向 UI 的 `TurnItem`。它处理用户消息、助手消息、推理内容、Web 搜索和图片生成等不同类型的响应项，并能识别上下文注入消息（如环境上下文、技能注入）以过滤出真正的用户内容。文件还提供了 developer 消息中上下文片段的检测功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CONTEXTUAL_DEVELOPER_PREFIXES` | 常量 | developer 消息中的上下文前缀列表（权限指令、模型切换、协作模式等） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_turn_item` | `(item: &ResponseItem) -> Option<TurnItem>` | 核心映射函数：将 ResponseItem 转换为 TurnItem |
| `parse_user_message` | `(message: &[ContentItem]) -> Option<UserMessageItem>` | 解析用户消息内容，过滤上下文注入消息 |
| `parse_agent_message` | `(id, message, phase) -> AgentMessageItem` | 解析助手消息内容 |
| `is_contextual_user_message_content` | `(message: &[ContentItem]) -> bool` | 判断用户消息是否包含上下文片段 |
| `is_contextual_dev_message_content` | `(message: &[ContentItem]) -> bool` | 判断 developer 消息是否包含可回滚的上下文片段 |
| `has_non_contextual_dev_message_content` | `(message: &[ContentItem]) -> bool` | 判断 developer 消息是否包含非上下文内容 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`TurnItem`、`ContentItem`、`ResponseItem` 等多种类型）、`crate::contextual_user_message`、`crate::web_search`
- **被引用方**: `lib.rs` 公开导出 `parse_turn_item`，广泛用于事件处理和历史解析

## 实现备注

- 用户消息中的图片标签（`<image>`、`<local_image>`）在解析时会被跳过，只保留实际的图片内容项
- Hook Prompt 消息的解析优先于普通用户消息
- 推理项（Reasoning）会分别收集摘要文本和原始内容文本
- Web 搜索项使用 `web_search_action_detail` 提取查询详情
- developer 消息前缀匹配使用大小写不敏感比较
