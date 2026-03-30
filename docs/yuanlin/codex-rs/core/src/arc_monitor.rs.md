# `arc_monitor.rs` — ARC 安全监控客户端

## 功能概述

该文件实现了 ARC（AI Risk Control）安全监控客户端，在模型发出工具调用之前将操作上下文发送到远程安全审查端点进行风险评估。`monitor_action` 函数构建包含对话历史、操作详情和元数据的请求，发送到 ARC 端点并解析返回的风险评估结果。根据评估结果返回三种决策：`Ok`（通过）、`SteerModel`（引导模型修正行为）、`AskUser`（要求用户确认）。该模块采用容错设计，任何网络或解析错误都默认放行。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ArcMonitorOutcome` | enum | 监控结果：Ok / SteerModel(原因) / AskUser(原因) |
| `ArcMonitorRequest` | struct | 监控请求体：元数据、消息历史、输入、策略、操作 |
| `ArcMonitorResult` | struct | 监控响应体：结果、原因、风险分数、风险级别、证据列表 |
| `ArcMonitorChatMessage` | struct | 聊天消息格式（角色 + 内容） |
| `ArcMonitorMetadata` | struct | 请求元数据：线程 ID、回合 ID、会话 ID、调用点 |
| `ArcMonitorResultOutcome` | enum | 结果类型：Ok / SteerModel / AskUser |
| `ArcMonitorRiskLevel` | enum | 风险级别：Low / Medium / High / Critical |
| `ArcMonitorEvidence` | struct | 风险证据：消息 + 原因 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `monitor_action` | `async fn(sess, turn_context, action, callsite) -> ArcMonitorOutcome` | 核心监控函数：构建请求、发送、解析结果 |
| `build_arc_monitor_request` | `async fn(sess, turn_context, action, callsite) -> ArcMonitorRequest` | 构建 ARC 请求体，含历史消息 |
| `build_arc_monitor_messages` | `fn(items) -> Vec<ArcMonitorChatMessage>` | 从会话历史构建 ARC 消息列表 |
| `build_arc_monitor_message_item` | `fn(item, index, ...) -> Option<ArcMonitorChatMessage>` | 单个历史项到 ARC 消息的映射 |

## 依赖关系

- **引用的 crate**: `reqwest`（HTTP 客户端）、`serde`/`serde_json`（序列化）、`tracing`
- **被引用方**: 工具执行层在调用 shell/函数/自定义工具前调用 `monitor_action`

## 实现备注

- 认证令牌优先从环境变量 `CODEX_ARC_MONITOR_TOKEN` 读取，其次使用 ChatGPT 认证令牌。
- 端点 URL 可通过 `CODEX_ARC_MONITOR_ENDPOINT_OVERRIDE` 环境变量覆盖。
- 消息历史构建时，只保留最后一个工具调用和最后一个加密推理内容，以减少请求体积。
- 过滤掉上下文用户消息（`is_contextual_user_message_content`）以避免噪声。
- 请求超时设为 30 秒，所有网络错误、解析错误、非成功状态码均默认返回 `ArcMonitorOutcome::Ok`（容错放行）。
- `SteerModel` 优先使用 `rationale`，`AskUser` 优先使用 `short_reason`，各自有兜底默认消息。
