# `output_parser.rs` -- Hook 命令输出的 JSON 解析器

## 功能概述

本文件负责将各类 Hook 命令的 stdout 输出（JSON 格式）解析为结构化的内部类型。每种事件类型（SessionStart、PreToolUse、PostToolUse、UserPromptSubmit、Stop）都有对应的解析函数和输出结构体。解析器还负责检测不支持的字段组合并生成相应的校验错误信息。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UniversalOutput` | struct | 所有事件共享的通用输出字段：continue_processing、stop_reason、suppress_output、system_message |
| `SessionStartOutput` | struct | 会话启动事件输出，含 universal 和 additional_context |
| `PreToolUseOutput` | struct | 工具使用前事件输出，含 block_reason 和 invalid_reason |
| `PostToolUseOutput` | struct | 工具使用后事件输出，含 should_block、reason、additional_context 等 |
| `UserPromptSubmitOutput` | struct | 用户提示提交事件输出，含 should_block、reason、additional_context 等 |
| `StopOutput` | struct | 停止事件输出，含 should_block 和 reason |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_session_start` | `fn(&str) -> Option<SessionStartOutput>` | 解析 SessionStart 事件的 JSON 输出 |
| `parse_pre_tool_use` | `fn(&str) -> Option<PreToolUseOutput>` | 解析 PreToolUse 事件的 JSON 输出，支持新旧两种 decision 格式 |
| `parse_post_tool_use` | `fn(&str) -> Option<PostToolUseOutput>` | 解析 PostToolUse 事件的 JSON 输出 |
| `parse_user_prompt_submit` | `fn(&str) -> Option<UserPromptSubmitOutput>` | 解析 UserPromptSubmit 事件的 JSON 输出 |
| `parse_stop` | `fn(&str) -> Option<StopOutput>` | 解析 Stop 事件的 JSON 输出 |
| `parse_json` | `fn<T>(&str) -> Option<T>` | 通用 JSON 解析辅助函数，仅接受 JSON object |

## 依赖关系

- **引用的 crate**: `serde`、`serde_json`
- **引用的内部模块**: `schema`（Wire 类型：SessionStartCommandOutputWire、PreToolUseCommandOutputWire 等）
- **被引用方**: `events::session_start`、`events::pre_tool_use`、`events::post_tool_use`、`events::user_prompt_submit`、`events::stop`

## 实现备注

- PreToolUse 支持两种 decision 格式：旧版的 `decision:block/approve` 和新版的 `hookSpecificOutput.permissionDecision:deny/allow/ask`。新版优先。
- PreToolUse 有多项不支持的字段检测（continue:false、stopReason、suppressOutput、updatedInput、additionalContext、permissionDecision:allow/ask）。
- PostToolUse 不支持 `suppressOutput` 和 `updatedMCPToolOutput`。
- block decision 必须提供非空 reason，否则标记为 invalid。
- `trimmed_reason` 辅助函数确保 reason 去除空白后非空。
