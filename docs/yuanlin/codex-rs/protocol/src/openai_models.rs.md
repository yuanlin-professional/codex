# `openai_models.rs` — OpenAI 模型元数据类型

## 功能概述

定义了 Codex 支持的 OpenAI 模型相关元数据类型，包括推理努力(reasoning effort)级别、输入模态(modality)、模型预设(preset)、模型可见性、工具类型、截断策略、模型信息及其升级配置等。这些类型在模型列表展示、模型切换、能力协商及指令生成中被广泛使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ReasoningEffort` | 枚举 | 推理努力级别（None / Minimal / Low / Medium / High / XHigh） |
| `InputModality` | 枚举 | 输入模态（Text / Image） |
| `ModelPreset` | 结构体 | 模型预设完整配置，包含显示名、默认推理级别、支持能力等 |
| `ModelInfo` | 结构体 | 后端返回的模型元数据，包含 slug、shell 类型、截断策略等 |
| `ModelVisibility` | 枚举 | 模型可见性（List / Hide / None） |
| `ConfigShellToolType` | 枚举 | Shell 执行能力类型（Default / Local / UnifiedExec / Disabled / ShellCommand） |
| `TruncationPolicyConfig` | 结构体 | 截断策略（按字节或按 token 截断，带限额） |
| `ModelMessages` | 结构体 | 模型指令模板与人格变量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ModelInfo::auto_compact_token_limit` | `fn auto_compact_token_limit(&self) -> Option<i64>` | 计算自动压缩 token 阈值 |
| `ModelInfo::get_model_instructions` | `fn get_model_instructions(&self, personality: Option<Personality>) -> String` | 根据人格生成模型指令 |
| `ModelPreset::filter_by_auth` | `fn filter_by_auth(models, chatgpt_mode) -> Vec<ModelPreset>` | 按认证模式过滤模型列表 |
| `ModelPreset::mark_default_by_picker_visibility` | `fn mark_default_by_picker_visibility(models: &mut [ModelPreset])` | 标记默认模型 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `strum`, `strum_macros`, `tracing`, `ts_rs`
- **被引用方**: `config_types.rs` 中引用 `ReasoningEffort`；`protocol.rs`、`app-server-protocol` 中大量使用

## 实现备注

- `ModelInfo` 到 `ModelPreset` 的转换实现了推理努力级别映射（nearest_effort 算法找最近支持级别）。
- 人格指令通过模板占位符 `{{ personality }}` 实现，当模板或变量缺失时回退到 base_instructions。
- 包含大量测试验证指令生成、人格消息获取、可用性 NUX 保持等边界场景。
