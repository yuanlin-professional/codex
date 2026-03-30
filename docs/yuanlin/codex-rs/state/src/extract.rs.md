# `extract.rs` — Rollout 条目到线程元数据的提取逻辑

## 功能概述
该文件实现了从 Rollout 条目（`RolloutItem`）中提取线程元数据的核心逻辑。`apply_rollout_item` 函数根据条目类型更新 `ThreadMetadata`：SessionMeta 设置来源/提供者/Git 信息、TurnContext 设置模型/策略、EventMsg::TokenCount 更新 token 使用量、EventMsg::UserMessage 提取首条用户消息和标题。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 纯函数模块 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_rollout_item` | `(metadata, item, default_provider)` | 将单个 rollout 条目应用到线程元数据 |
| `rollout_item_affects_thread_metadata` | `(item) -> bool` | 判断条目是否会影响线程元数据 |
| `enum_to_string` | `(value: &T) -> String` | 将可序列化枚举转为字符串 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `serde`, `serde_json`
- **被引用方**: `runtime/threads.rs`, `codex-rollout/metadata.rs`

## 实现备注
- SessionMeta 中不匹配 thread ID 的条目会被忽略（处理 fork 场景）
- TurnContext 仅在 SessionMeta 未设置 cwd 时覆盖 cwd
- 用户消息会去除 `USER_MESSAGE_BEGIN` 前缀
- 纯图片消息使用 `[Image]` 占位符
