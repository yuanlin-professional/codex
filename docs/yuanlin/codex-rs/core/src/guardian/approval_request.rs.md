# `approval_request.rs` — Guardian 审批请求数据模型与序列化

## 功能概述

该模块定义了 Guardian 审批请求的核心数据模型（`GuardianApprovalRequest`）及其 JSON 序列化逻辑。它支持多种审批类型：Shell 命令、ExecCommand、Execve（Unix）、ApplyPatch、NetworkAccess、McpToolCall。模块还提供了请求的 JSON 格式化、评估摘要提取、请求 ID/turn ID 获取以及格式化友好展示等功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GuardianApprovalRequest` | enum | 审批请求枚举，包含 Shell/ExecCommand/Execve/ApplyPatch/NetworkAccess/McpToolCall 六种变体 |
| `GuardianMcpAnnotations` | struct | MCP 工具调用的安全注解，含 `destructive_hint`、`open_world_hint`、`read_only_hint` |
| `CommandApprovalAction` | struct (私有) | 命令类审批的序列化辅助结构 |
| `ExecveApprovalAction` | struct (私有, Unix) | execve 类审批的序列化辅助结构 |
| `McpToolCallApprovalAction` | struct (私有) | MCP 工具调用审批的序列化辅助结构 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `guardian_approval_request_to_json` | `fn(action) -> Result<Value>` | 将审批请求序列化为完整的 JSON 值 |
| `guardian_assessment_action_value` | `fn(action) -> Value` | 生成评估用的简化 action 摘要（如 ApplyPatch 省略 patch 内容） |
| `guardian_request_id` | `fn(request) -> &str` | 提取请求的唯一标识 ID |
| `guardian_request_turn_id` | `fn(request, default) -> &str` | 提取请求关联的 turn ID，NetworkAccess 使用自身 turn_id，其余使用默认值 |
| `format_guardian_action_pretty` | `fn(action) -> Result<String>` | 格式化为美化的 JSON 字符串，对长字符串字段进行截断 |
| `truncate_guardian_action_value` | `fn(value) -> Value` | 递归截断 JSON 值中超长的字符串字段 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`PermissionProfile`、`NetworkApprovalProtocol`）、`codex_utils_absolute_path`、`codex_shell_command`、`serde`、`serde_json`
- **引用的内部模块**: `super::GUARDIAN_MAX_ACTION_STRING_TOKENS`、`super::prompt::guardian_truncate_text`、`crate::sandboxing::SandboxPermissions`
- **被引用方**: `guardian::review`、`guardian::prompt`、`guardian::tests`

## 实现备注

- **安全摘要**: `guardian_assessment_action_value` 有意省略 `ApplyPatch` 的 patch 文本，避免将完整补丁内容暴露给评估事件流。
- **截断策略**: `truncate_guardian_action_value` 递归遍历 JSON 值树，对所有字符串叶节点应用 token 级别的截断，并按键名排序对象字段以确保确定性输出。
- **命令连接**: Shell 和 ExecCommand 的评估摘要使用 `shlex_join` 将命令数组连接为单个字符串。
