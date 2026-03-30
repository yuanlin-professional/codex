# `request_user_input.rs` — 用户输入请求工具处理器

## 功能概述
该文件实现了 `request_user_input` 工具，允许模型向用户提出一到三个简短问题并等待回答。此工具仅在特定的协作模式下可用（默认仅限 Plan 模式，可通过 feature flag 扩展到 Default 模式）。每个问题必须提供非空的选项列表，并自动启用 "其他" 自由输入选项。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestUserInputHandler` | struct | 处理器，包含 `default_mode_request_user_input` 布尔字段控制 Default 模式下的可用性 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `request_user_input_is_available` | `fn request_user_input_is_available(mode, default_mode_request_user_input) -> bool` | 判断给定模式下工具是否可用 |
| `format_allowed_modes` | `fn format_allowed_modes(default_mode_request_user_input) -> String` | 格式化允许使用该工具的模式列表为人类可读文本 |
| `request_user_input_unavailable_message` | `pub(crate) fn request_user_input_unavailable_message(mode, flag) -> Option<String>` | 如果在当前模式不可用则返回错误消息，否则返回 None |
| `request_user_input_tool_description` | `pub(crate) fn request_user_input_tool_description(flag) -> String` | 生成包含可用模式信息的工具描述 |
| `handle` | `async fn handle(&self, invocation) -> Result<FunctionToolOutput, FunctionCallError>` | 验证模式可用性、解析参数、检查选项非空、设置 `is_other` 标志、发起用户输入请求 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol`（`ModeKind`, `TUI_VISIBLE_COLLABORATION_MODES`, `RequestUserInputArgs`）
- **内部依赖**: `tools::context`, `tools::registry`, `tools::handlers::parse_arguments`
- **被引用方**: 工具注册表中注册为 `request_user_input` 工具

## 实现备注
- 模式可用性遵循两级规则：Plan 模式始终可用；Default 模式可通过 feature flag 启用
- 每个问题的 `is_other` 字段被强制设为 `true`，允许用户自由输入
- 请求被取消时（返回 `None`）会报告错误
