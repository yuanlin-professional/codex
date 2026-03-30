# `models.rs` — 模型列表查询与预设到协议类型的转换

## 功能概述

本模块提供 `supported_models` 函数从 `ThreadManager` 获取已缓存或在线刷新的模型预设列表，并将其转换为 `codex_app_server_protocol::Model` 类型。转换过程包括模型 ID、显示名称、描述、升级信息、可用性提示、推理力度选项、输入模态、personality 支持等字段的映射。可通过 `include_hidden` 参数控制是否包含不在 picker 中显示的隐藏模型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义结构体 | — | 仅包含转换函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `supported_models` | `async fn(thread_manager, include_hidden) -> Vec<Model>` | 获取并转换支持的模型列表 |
| `model_from_preset` | `fn(preset) -> Model` | 将单个 `ModelPreset` 转换为 `Model` |
| `reasoning_efforts_from_preset` | `fn(efforts) -> Vec<ReasoningEffortOption>` | 转换推理力度预设列表 |

## 依赖关系

- **引用的 crate**: `codex_core`（`ThreadManager`）、`codex_core::models_manager`、`codex_app_server_protocol`（`Model`）、`codex_protocol::openai_models`
- **被引用方**: `codex_message_processor.rs`（处理 `model/list` 请求时调用）

## 实现备注

- 使用 `RefreshStrategy::OnlineIfUncached` 策略：若有缓存则直接使用，否则在线获取。
