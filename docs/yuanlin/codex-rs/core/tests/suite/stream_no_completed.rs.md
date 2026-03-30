# `stream_no_completed.rs` -- 测试模块

## 测试覆盖范围
验证当 SSE 流在未发送 `response.completed` 事件时就终止，agent 能正确检测并重试请求。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `retries_on_early_close` | SSE 流提前关闭（缺少 completed 事件）时，agent 重试并在第二次获得完整响应 |
