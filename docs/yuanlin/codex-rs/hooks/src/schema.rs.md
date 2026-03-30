# `schema.rs` -- Hooks JSON Schema 定义与 fixture 生成

## 功能概述

该文件定义了钩子系统所有事件的 JSON Schema wire 格式，包括 pre-tool-use、post-tool-use、session-start、user-prompt-submit 和 stop 五类事件的输入/输出 schema。使用 `schemars` crate 自动从 Rust 结构体生成 Draft-07 JSON Schema，并提供 fixture 文件的生成和验证功能。schema 确保钩子命令的输入/输出格式在版本间保持稳定。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `NullableString` | struct | 可空字符串类型，JSON Schema 中表示为 `string | null` |
| `HookUniversalOutputWire` | struct | 通用输出字段：continue、stopReason、suppressOutput、systemMessage |
| `HookEventNameWire` | enum | 事件名枚举：PreToolUse、PostToolUse、SessionStart、UserPromptSubmit、Stop |
| `PreToolUseCommandInput` | struct | pre-tool-use 命令输入 schema |
| `PreToolUseCommandOutputWire` | struct | pre-tool-use 命令输出 schema |
| `PostToolUseCommandInput` | struct | post-tool-use 命令输入 schema |
| `PostToolUseCommandOutputWire` | struct | post-tool-use 命令输出 schema |
| `SessionStartCommandInput` | struct | session-start 命令输入 schema |
| `SessionStartCommandOutputWire` | struct | session-start 命令输出 schema |
| `UserPromptSubmitCommandInput` | struct | user-prompt-submit 命令输入 schema |
| `UserPromptSubmitCommandOutputWire` | struct | user-prompt-submit 命令输出 schema |
| `StopCommandInput` | struct | stop 命令输入 schema |
| `StopCommandOutputWire` | struct | stop 命令输出 schema |
| `PreToolUseDecisionWire` | enum | pre-tool-use 决策：approve 或 block |
| `PreToolUsePermissionDecisionWire` | enum | pre-tool-use 权限决策：allow、deny、ask |
| `BlockDecisionWire` | enum | 通用阻止决策：block |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_schema_fixtures` | `fn(schema_root: &Path) -> Result<()>` | 生成所有事件类型的 JSON Schema fixture 文件 |
| `schema_json<T>` | `fn() -> Result<Vec<u8>>` | 将类型的 JSON Schema 序列化为格式化 JSON |
| `canonicalize_json` | `fn(value: &Value) -> Value` | 递归排序 JSON 对象键以确保输出一致性 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `serde_json`
- **被引用方**: `events/pre_tool_use.rs`, `events/post_tool_use.rs`, `events/stop.rs` 等事件模块，以及 `bin/write_hooks_schema_fixtures.rs`

## 实现备注

- Codex 在 schema 中扩展了 `turn_id` 字段（Claude 公共文档中无此字段），用于内部 turn 级别的钩子消费
- `canonicalize_json` 确保 JSON 对象键按字典序排列，使 fixture 文件在不同平台上完全一致
- 包含测试验证生成的 schema 与仓库中的 fixture 文件一致，以及所有 turn-scoped 输入 schema 包含 `turn_id` 字段
