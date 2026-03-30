# `session.rs` — 会话级可变状态管理

## 功能概述

`session.rs` 定义了 `SessionState` 结构体，用于管理整个会话周期内的可变状态。它封装了对话历史（通过 `ContextManager`）、Token 使用信息、速率限制快照、MCP 依赖状态、连接器选择、权限授予等状态数据，并提供了一系列便利方法来读写这些状态。该模块还实现了速率限制快照的合并逻辑，确保在新快照缺少部分字段时能从先前快照中保留。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionState` | struct | 会话级持久化可变状态，包含历史记录、Token 信息、速率限制、依赖环境变量、权限等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(session_configuration: SessionConfiguration) -> Self` | 创建新的会话状态实例 |
| `record_items` | `fn record_items<I>(&mut self, items: I, policy: TruncationPolicy)` | 将响应条目记录到历史中 |
| `replace_history` | `fn replace_history(&mut self, items, reference_context_item)` | 替换整个历史记录（用于 compact 等场景） |
| `set_rate_limits` | `fn set_rate_limits(&mut self, snapshot: RateLimitSnapshot)` | 设置速率限制，合并缺失字段 |
| `update_token_info_from_usage` | `fn update_token_info_from_usage(&mut self, usage, model_context_window)` | 根据 API 返回的 usage 更新 token 信息 |
| `merge_connector_selection` | `fn merge_connector_selection<I>(&mut self, connector_ids: I) -> HashSet<String>` | 合并连接器 ID 到活跃集合并返回合并后结果 |
| `record_granted_permissions` | `fn record_granted_permissions(&mut self, permissions: PermissionProfile)` | 记录已授予的权限，与已有权限合并 |
| `merge_rate_limit_fields` | `fn merge_rate_limit_fields(previous, snapshot) -> RateLimitSnapshot` | 合并速率限制字段，缺失的 `limit_id` 默认为 "codex" |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_sandboxing`, `codex_utils_output_truncation`, `codex_hooks`
- **引用的内部模块**: `ContextManager`, `SessionConfiguration`, `PreviousTurnSettings`, `RateLimitSnapshot`, `TokenUsage`, `TokenUsageInfo`, `SessionStartupPrewarmHandle`, `TurnContextItem`
- **被引用方**: `Session` 持有 `SessionState` 来管理对话级状态

## 实现备注

- `merge_rate_limit_fields` 辅助函数确保 `limit_id` 缺失时默认填充为 `"codex"`，并从前一快照继承 `credits` 和 `plan_type`
- `previous_turn_settings` 字段为私有，通过 getter/setter 访问，用于轮次间模型/实时处理状态的延续
- `granted_permissions` 使用 `merge_permission_profiles` 实现权限的增量合并
- 通过 `#[path = "session_tests.rs"]` 引入独立测试文件
