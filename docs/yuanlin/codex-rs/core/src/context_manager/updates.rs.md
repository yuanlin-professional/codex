# `updates.rs` — 轮次间上下文更新构建器

## 功能概述

`updates.rs` 负责在连续的对话轮次之间构建差异化的上下文更新消息。当会话设置（如工作目录、权限策略、协作模式、实时状态、模型人格、模型指令等）在两轮之间发生变化时，该模块会生成相应的 developer 或 user 消息注入到对话历史中，使模型能够感知到这些变化。这种差异化更新机制避免了每次都重新注入完整上下文，降低了 token 消耗。

## 关键结构体/枚举

无独立结构体/枚举定义。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_settings_update_items` | `fn build_settings_update_items(previous, previous_turn_settings, next, shell, exec_policy, personality_feature_enabled) -> Vec<ResponseItem>` | 主入口：构建两轮之间所有设置变更的更新消息列表 |
| `build_environment_update_item` | `fn build_environment_update_item(previous, next, shell) -> Option<ResponseItem>` | 构建环境变化（如 cwd 变更）的 user 消息 |
| `build_permissions_update_item` | `fn build_permissions_update_item(previous, next, exec_policy) -> Option<DeveloperInstructions>` | 构建权限策略变化的 developer 指令 |
| `build_collaboration_mode_update_item` | `fn build_collaboration_mode_update_item(previous, next) -> Option<DeveloperInstructions>` | 构建协作模式变化的 developer 指令 |
| `build_realtime_update_item` | `fn build_realtime_update_item(previous, previous_turn_settings, next) -> Option<DeveloperInstructions>` | 构建实时状态切换（激活/停用）的 developer 指令 |
| `build_personality_update_item` | `fn build_personality_update_item(previous, next, personality_feature_enabled) -> Option<DeveloperInstructions>` | 构建模型人格变更的 developer 指令 |
| `build_model_instructions_update_item` | `fn build_model_instructions_update_item(previous_turn_settings, next) -> Option<DeveloperInstructions>` | 构建模型切换时的模型指令 developer 消息 |
| `personality_message_for` | `fn personality_message_for(model_info, personality) -> Option<String>` | 获取指定模型和人格对应的人格消息文本 |
| `build_developer_update_item` | `fn build_developer_update_item(text_sections) -> Option<ResponseItem>` | 将多个 developer 指令文本合并为一条 developer 消息 |
| `build_contextual_user_message` | `fn build_contextual_user_message(text_sections) -> Option<ResponseItem>` | 将多个上下文文本合并为一条 user 消息 |
| `build_initial_realtime_item` | `fn build_initial_realtime_item(previous, previous_turn_settings, next) -> Option<DeveloperInstructions>` | 构建初始实时状态指令（委托给 `build_realtime_update_item`） |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ContentItem`, `DeveloperInstructions`, `ResponseItem`, `ModelInfo`, `TurnContextItem` 等）、`codex_execpolicy`、`codex_features`
- **引用的内部模块**: `PreviousTurnSettings`、`TurnContext`、`EnvironmentContext`、`Shell`
- **被引用方**: 会话的 turn 构建流程在准备发送给模型的消息时调用 `build_settings_update_items`

## 实现备注

- 所有更新构建函数都依赖 `previous`（`TurnContextItem`）和 `next`（`TurnContext`）的比较来决定是否生成更新消息
- `build_settings_update_items` 将所有 developer 指令合并为一条消息，模型切换指令排在最前以确保模型特定指导先被阅读
- 实时状态（realtime）的切换逻辑需要同时考虑 `TurnContextItem` 中的状态和 `PreviousTurnSettings` 中的状态，处理首次轮次和恢复场景
- 人格更新仅在模型未切换时生效（模型切换时模型指令中已包含人格信息）
- 当 `previous` 为 `None` 时，所有差异构建函数返回 `None`，由上游的完整上下文注入逻辑处理
