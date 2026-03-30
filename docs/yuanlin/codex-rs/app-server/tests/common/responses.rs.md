# `responses.rs` — SSE 响应构建工具

## 功能概述
提供一系列函数用于构建模拟的 OpenAI Responses API SSE 响应体。支持构建 shell_command 调用、
apply_patch 调用、exec_command 调用、request_user_input 调用、request_permissions 调用
以及最终助手消息等多种工具调用响应格式。每个函数返回格式化的 SSE 事件字符串。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_shell_command_sse_response` | `pub fn ...(command, workdir, timeout_ms, call_id) -> Result<String>` | 构建 shell_command 工具调用的 SSE 响应 |
| `create_final_assistant_message_sse_response` | `pub fn ...(message: &str) -> Result<String>` | 构建包含助手最终消息的 SSE 响应 |
| `create_apply_patch_sse_response` | `pub fn ...(patch_content, call_id) -> Result<String>` | 构建 apply_patch 工具调用的 SSE 响应 |
| `create_exec_command_sse_response` | `pub fn ...(call_id) -> Result<String>` | 构建 exec_command 工具调用的 SSE 响应 |
| `create_request_user_input_sse_response` | `pub fn ...(call_id) -> Result<String>` | 构建 request_user_input 工具调用的 SSE 响应 |
| `create_request_permissions_sse_response` | `pub fn ...(call_id) -> Result<String>` | 构建 request_permissions 工具调用的 SSE 响应 |

## 依赖关系
- **引用的 crate**: `core_test_support::responses`, `serde_json`, `shlex`
- **被引用方**: `common/lib.rs` 导出，供 turn_start、request_user_input 等测试使用
