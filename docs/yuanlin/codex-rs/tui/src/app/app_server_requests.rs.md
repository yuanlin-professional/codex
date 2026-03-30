# `app_server_requests.rs` — App-server 待处理请求管理

## 功能概述

本模块管理 TUI 和 app-server 之间的待处理请求（pending requests），包括命令执行审批、文件变更审批、权限请求、用户输入请求和 MCP elicitation 请求。它通过 `PendingAppServerRequests` 跟踪请求 ID 映射，并在用户操作时将操作解析为对应的 app-server 响应。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PendingAppServerRequests` | struct | 跟踪各类待处理请求的 HashMap 集合 |
| `AppServerRequestResolution` | struct | 请求解析结果，包含 request_id 和序列化的响应值 |
| `UnsupportedAppServerRequest` | struct | 不支持的请求类型，包含 request_id 和拒绝原因 |
| `McpLegacyRequestKey` | struct | MCP 请求的复合键（server_name + request_id） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `note_server_request` | `(&mut self, request) -> Option<Unsupported>` | 注册新请求，对不支持的类型返回拒绝信息 |
| `take_resolution` | `(&mut self, op) -> Result<Option<Resolution>>` | 根据用户操作查找并移除匹配的待处理请求 |
| `resolve_notification` | `(&mut self, request_id)` | 根据服务器通知清除已解决的请求 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `codex_protocol`
- **被引用方**: `App` 在处理审批操作时调用

## 实现备注

- `DynamicToolCall` 和旧式 `ApplyPatchApproval`/`ExecCommandApproval` 被标记为不支持
- MCP elicitation 使用复合键（server_name + request_id）进行关联
- 文件变更审批对无效决策（如 execpolicy 修正）返回错误
