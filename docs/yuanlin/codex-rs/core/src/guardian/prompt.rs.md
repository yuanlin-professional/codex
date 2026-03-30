# `prompt.rs` — Guardian 审查提示词构建与转录处理

## 功能概述

该模块负责构建 Guardian 审查的完整提示词内容，包括：从 session 历史中提取和过滤对话转录条目、按 token 预算裁剪转录内容、组装审查请求的用户消息项。模块还提供了文本截断工具、Guardian 评估输出的 JSON schema 定义、策略提示词加载以及评估结果的解析功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GuardianTranscriptEntry` | struct | 过滤后的转录条目，含 `kind`（角色类型）和 `text`（内容） |
| `GuardianTranscriptEntryKind` | enum | 转录条目类型：`User`、`Assistant`、`Tool(String)` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_guardian_prompt_items` | `async fn(session, retry_reason, request) -> Result<Vec<UserInput>>` | 构建完整的 Guardian 审查用户消息项列表 |
| `collect_guardian_transcript_entries` | `fn(items) -> Vec<GuardianTranscriptEntry>` | 从 ResponseItem 列表中提取可读对话和工具调用/结果条目，跳过合成的上下文消息 |
| `render_guardian_transcript_entries` | `fn(entries) -> (Vec<String>, Option<String>)` | 按 token 预算渲染转录条目，返回格式化文本和可选的省略说明 |
| `guardian_truncate_text` | `fn(content, token_cap) -> String` | 将文本截断到指定 token 上限，保留前后部分并插入 XML 截断标记 |
| `parse_guardian_assessment` | `fn(text) -> Result<GuardianAssessment>` | 解析 Guardian 评估 JSON，支持从包裹的散文中提取嵌入的 JSON |
| `guardian_output_schema` | `fn() -> Value` | 返回强制 Guardian 输出结构化 JSON 的 schema |
| `guardian_policy_prompt` | `fn() -> String` | 加载 Guardian 策略提示词（从 `policy.md` 文件）并附加输出契约 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_utils_output_truncation`、`serde_json`
- **引用的内部模块**: `crate::codex::Session`、`crate::compact::content_items_to_text`、`crate::event_mapping`、`super::approval_request`
- **被引用方**: `guardian::review`（调用 `build_guardian_prompt_items`）

## 实现备注

- **转录裁剪策略**: 简单且可审计——始终保留用户消息（承载授权和意图），然后从最新向最旧遍历非用户条目，在消息/工具 token 预算允许的范围内保留。消息和工具使用独立预算，防止工具证据挤占人类对话内容。
- **截断机制**: `guardian_truncate_text` 按 token 近似字节数估算，保留前半和后半并在中间插入 `<truncated omitted_approx_tokens="N" />` XML 标记，正确处理 UTF-8 多字节字符边界。
- **JSON 解析容错**: `parse_guardian_assessment` 先尝试直接解析，失败后尝试从文本中提取首个 `{...}` 块，提高对模型输出格式漂移的容错性。
- **策略提示词**: 策略内容存储在独立的 `policy.md` 文件中，方便审计和审查，输出契约从代码中附加以保持与 schema 的一致性。
