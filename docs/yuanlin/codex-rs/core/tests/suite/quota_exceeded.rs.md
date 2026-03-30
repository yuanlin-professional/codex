# `quota_exceeded.rs` -- 测试模块

## 测试覆盖范围
测试当 API 返回配额超限（insufficient_quota）错误时，Codex 核心层的事件处理行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `quota_exceeded_emits_single_error_event` | 当 response.failed 包含 insufficient_quota 错误时，仅发出一个 Codex:Error 事件，消息为 "Quota exceeded. Check your plan and billing details."，之后正常完成 turn |
