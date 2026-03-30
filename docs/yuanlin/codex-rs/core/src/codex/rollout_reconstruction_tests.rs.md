# `codex/rollout_reconstruction_tests.rs` — Rollout 重建逻辑的测试套件

## 功能概述

该文件包含 `rollout_reconstruction.rs` 中会话历史重建逻辑的全面测试套件。通过构造不同的 `RolloutItem` 序列来验证各种边界场景下重建算法的正确性，包括：裸 `TurnContext` 不应恢复上轮设置、缺失 turn_id 的上下文恢复、回滚操作对历史和元数据的同步影响、非用户轮次的跳过逻辑、跨代理助手轮次的计数、回滚超出用户轮次数的清空行为等。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `user_message` | `fn(text: &str) -> ResponseItem` | 构造用户角色测试消息 |
| `assistant_message` | `fn(text: &str) -> ResponseItem` | 构造助手角色测试消息 |
| `inter_agent_assistant_message` | `fn(text: &str) -> ResponseItem` | 构造跨代理通信的助手消息 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`AgentPath`、`ThreadId`、`ContentItem`、`ResponseItem`、`InterAgentCommunication` 等）、`pretty_assertions`
- **引用的模块**: 上级模块的 `Session`、`TurnContextItem`、`PreviousTurnSettings`、`CompactedItem`、`EventMsg` 等

## 实现备注

测试用例覆盖以下关键场景：

- **裸 TurnContext 处理**：无生命周期事件（TurnStarted/TurnComplete）包围的 TurnContext 不应恢复 `previous_turn_settings` 或设置 `reference_context_item`
- **回滚同步**：已完成轮次和未完成轮次的回滚都能正确同步历史记录和元数据
- **非用户轮次跳过**：独立的非用户轮次（如无 UserMessage 的自动任务轮次）不消耗回滚计数
- **跨代理轮次计数**：包含 `InterAgentCommunication` 的助手指令轮次被正确计入回滚
- **压缩交互**：压缩操作（Compacted）与回滚的组合、旧式无 replacement_history 压缩的兼容处理
- **中断轮次**：TurnAborted 事件（带或不带 turn_id）对活跃段的影响
- **尾部不完整轮次**：未结束的轮次的 TurnContext 保留以及压缩后的清除行为
