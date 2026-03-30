# `model_info.rs` -- 模型信息构建与配置覆盖

## 功能概述

提供模型元数据的构建和配置覆盖功能。`model_info_from_slug` 为未知模型 slug 构建回退用的最小化 `ModelInfo` 描述符，包含合理的默认值。`with_config_overrides` 将用户配置中的覆盖项（如推理摘要支持、上下文窗口大小、工具输出 token 限制、基础指令等）应用到模型信息上。还定义了本地个性化消息模板，支持 friendly 和 pragmatic 两种个性风格。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （无自定义结构体） | — | 使用 `codex_protocol` 中的 `ModelInfo` 和相关类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_config_overrides` | `fn(ModelInfo, &Config) -> ModelInfo` | 将配置中的覆盖项应用到模型信息 |
| `model_info_from_slug` | `fn(&str) -> ModelInfo` | 为未知 slug 构建回退 ModelInfo（标记 used_fallback_model_metadata=true） |
| `local_personality_messages_for_slug` | `fn(&str) -> Option<ModelMessages>` | 为特定 slug 返回本地个性化消息模板 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（ModelInfo 及相关类型）、`codex_features`、`codex_utils_output_truncation`
- **被引用方**: `manager.rs` 在模型查找未命中远程列表时调用 `model_info_from_slug`；`with_config_overrides` 在每次模型信息构建时调用

## 实现备注

- 回退模型使用 272,000 token 上下文窗口、95% 有效窗口比例、10,000 字节截断策略。
- 配置覆盖中 `model_supports_reasoning_summaries` 仅在为 true 时生效（false 不会禁用已支持的模型）。
- 工具输出 token 限制会根据截断模式（字节/token）转换为对应的限额值。
- 当设置了自定义 `base_instructions` 时，`model_messages` 会被清除，确保指令不冲突。
- 个性化模板仅对 `gpt-5.2-codex` 和 `exp-codex-personality` 两个 slug 生效。
