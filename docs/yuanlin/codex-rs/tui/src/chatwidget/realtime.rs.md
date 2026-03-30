# `realtime.rs` — 实时语音对话 UI 状态管理

## 功能概述

本模块管理 TUI 中实时语音对话（realtime voice conversation）的 UI 状态和生命周期。它处理语音对话的启动、停止、音频捕获（麦克风）和播放（扬声器），以及实时事件（转录增量、音频输出、错误等）的响应。在 Linux 平台上，音频功能被禁用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RealtimeConversationPhase` | enum | 语音对话阶段：Inactive、Starting、Active、Stopping |
| `RealtimeConversationUiState` | struct | 语音对话 UI 状态，包含阶段、会话 ID、音频捕获/播放句柄 |
| `RenderedUserMessageEvent` | struct | 已渲染的用户消息事件（用于去重） |
| `PendingSteerCompareKey` | struct | 用于比较 pending steer 的简化键 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_realtime_conversation` | `(&mut self)` | 启动实时语音对话 |
| `on_realtime_conversation_started` | `(&mut self, ev)` | 处理语音对话启动成功事件 |
| `on_realtime_conversation_realtime` | `(&mut self, ev)` | 处理实时音频事件（语音开始、转录、音频输出等） |
| `stop_realtime_conversation_from_ui` | `(&mut self)` | 用户主动停止语音对话 |
| `maybe_defer_user_message_for_realtime` | `(&mut self, msg) -> Option<UserMessage>` | 在语音模式下拦截文本提交 |

## 依赖关系

- **引用的 crate**: `codex_protocol::protocol` (Realtime* 事件类型)
- **被引用方**: `ChatWidget` 语音对话相关事件处理

## 实现备注

- 非 Linux 平台使用 `crate::voice` 模块进行音频捕获和播放
- 语音模式下文本提交被拦截并恢复到 composer
- 麦克风捕获在后台线程中运行，通过 `AtomicBool` 停止标志控制
- 音频播放器在首次需要时延迟初始化
