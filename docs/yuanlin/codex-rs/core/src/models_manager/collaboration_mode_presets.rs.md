# `collaboration_mode_presets.rs` -- 协作模式预设配置

## 功能概述

定义内置的协作模式预设（Collaboration Mode Presets），目前包含 Plan 模式和 Default 模式。每种模式有独立的开发者指令模板，支持模板变量替换（已知模式名称列表、request_user_input 可用性提示、提问指导）。Default 模式的行为可通过 `CollaborationModesConfig` 特性标志控制是否启用 `request_user_input` 工具。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CollaborationModesConfig` | 结构体 | 协作模式特性标志，包含 `default_mode_request_user_input` 开关 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `builtin_collaboration_mode_presets` | `fn(CollaborationModesConfig) -> Vec<CollaborationModeMask>` | 返回所有内置协作模式预设 |
| `plan_preset` | `fn() -> CollaborationModeMask` | 构建 Plan 模式预设（推理强度 Medium） |
| `default_preset` | `fn(CollaborationModesConfig) -> CollaborationModeMask` | 构建 Default 模式预设（含模板渲染的开发者指令） |
| `default_mode_instructions` | `fn(CollaborationModesConfig) -> String` | 渲染 Default 模式的开发者指令模板 |
| `format_mode_names` | `fn(&[ModeKind]) -> String` | 将模式名称列表格式化为自然语言（用 "and" 连接） |
| `request_user_input_availability_message` | `fn(ModeKind, bool) -> String` | 生成 request_user_input 工具可用性说明 |
| `asking_questions_guidance_message` | `fn(bool) -> String` | 生成提问行为指导（使用工具 vs 纯文本提问） |

## 依赖关系

- **引用的 crate**: `codex_protocol::config_types`、`codex_protocol::openai_models::ReasoningEffort`、`codex_utils_template`
- **被引用方**: `manager.rs` 中的 `ModelsManager::list_collaboration_modes` 调用

## 实现备注

- Plan 模式使用静态 Markdown 模板（`collaboration_mode/plan.md`），Default 模式使用动态渲染模板。
- 模板变量包括 `KNOWN_MODE_NAMES`、`REQUEST_USER_INPUT_AVAILABILITY`、`ASKING_QUESTIONS_GUIDANCE`。
- 当 `default_mode_request_user_input` 启用时，Default 模式建议使用 `request_user_input` 工具而非纯文本提问。
- 模板解析在 `LazyLock` 中完成，解析失败会 panic，确保嵌入模板的正确性在启动时即被发现。
