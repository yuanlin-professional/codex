# `mcp_tool_call.rs` — MCP 工具调用处理与审批逻辑

## 功能概述

`mcp_tool_call.rs`（约 1604 行）实现了 MCP 工具调用的完整处理流程，包括参数解析、审批决策（自动批准/提示/Guardian 审核/ARC 监控）、实际工具调用执行、结果清洗、遥测上报以及持久化审批记忆。它同时处理 Codex Apps 工具和自定义 MCP 服务器工具，支持连接器级别和工具级别的细粒度审批策略，以及 elicitation 请求（MCP 服务器发起的交互式确认）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `McpToolApprovalDecision` | enum | 审批决策：Accept / AcceptForSession / AcceptAndRemember / Decline / Cancel / BlockedBySafetyMonitor |
| `McpToolApprovalMetadata` | struct | 工具审批元数据：annotations、connector_id/name/description、tool_title/description、codex_apps_meta |
| `McpToolApprovalKey` | struct | 审批记忆键：server + connector_id + tool_name |
| `McpToolApprovalPromptOptions` | struct | 审批提示选项：allow_session_remember、allow_persistent_approval |
| `McpToolApprovalElicitationRequest` | struct | 审批 elicitation 请求参数 |
| `McpToolCallSpanFields` | struct | 工具调用 span 字段 |
| `McpAppUsageMetadata` | struct | App 使用追踪元数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_mcp_tool_call` | `async fn handle_mcp_tool_call(sess, turn_context, call_id, server, tool_name, arguments) -> CallToolResult` | MCP 工具调用主入口：解析参数、确定审批模式、执行审批流程、调用工具、返回结果 |
| `maybe_request_mcp_tool_approval` | `async fn ...` | 判断是否需要审批并执行审批流程（Guardian/elicitation/RequestUserInput） |
| `lookup_mcp_tool_metadata` | `async fn ...` | 查询工具元数据（annotations、connector 信息） |
| `requires_mcp_tool_approval` | `fn ...` | 根据 annotations 判断是否需要审批（destructive_hint、read_only_hint） |
| `is_full_access_mode` | `fn ...` | 判断是否为完全访问模式（Never + DangerFullAccess） |
| `custom_mcp_tool_approval_mode` | `fn ...` | 获取自定义 MCP 工具的审批模式（从 config 读取） |
| `build_guardian_mcp_tool_review_request` | `fn ...` | 构建 Guardian 审核请求 |
| `sanitize_mcp_tool_result_for_model` | `fn ...` | 清洗工具结果（不支持图片输入的模型替换 image 内容） |
| `apply_mcp_tool_approval_decision` | `async fn ...` | 应用审批决策（会话记忆/持久化） |
| `persist_codex_app_tool_approval` / `persist_custom_mcp_tool_approval` | `async fn ...` | 持久化审批到 config.toml |
| `build_mcp_tool_approval_question` | `fn ...` | 构建审批问题（UI 呈现） |
| `build_mcp_tool_approval_elicitation_request` | `fn ...` | 构建审批 elicitation 请求 |
| `parse_mcp_tool_approval_elicitation_response` / `parse_mcp_tool_approval_response` | `fn ...` | 解析审批响应 |
| `maybe_track_codex_app_used` | `async fn ...` | 追踪 Codex App 使用分析事件 |
| `emit_mcp_call_metrics` | `fn ...` | 发出 MCP 调用指标 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（审批和 MCP 类型）、`codex_analytics`（分析事件）、`codex_features`（特性开关）、`rmcp`（ToolAnnotations）、`codex_rmcp_client`（ElicitationResponse）
- **被引用方**: `codex.rs`（`run_turn` 中通过 ToolRouter 分发到此模块）

## 实现备注

1. **审批策略层级**：Codex Apps 工具使用 `app_tool_policy`（基于 config 中的 apps 配置），自定义 MCP 工具使用 `custom_mcp_tool_approval_mode`（基于 mcp_servers 配置）。
2. **ARC 监控**：对自动批准的工具调用通过 `monitor_action` 进行安全监控，可能升级为用户确认或直接阻止。
3. **会话记忆 vs 持久化**：`AcceptForSession` 仅在内存中记忆，`AcceptAndRemember` 写入 config.toml 并重新加载配置层。
4. **Guardian 审核**：当 `routes_approval_to_guardian` 为 true 时，审批请求路由到 Guardian 子代理而非直接向用户提问。
5. **Elicitation 优先路径**：启用 `ToolCallMcpElicitation` 特性时，审批通过 McpServerElicitation 机制而非传统的 RequestUserInput。
