# `stream_error_allows_next_turn.rs` -- 测试模块

## 测试覆盖范围
测试当 API 流请求失败（500 错误）后，会话能正确释放并允许后续 turn 正常进行。验证错误恢复机制不会阻塞后续的用户输入。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `continue_after_stream_error` | 第一个请求返回 500 错误，触发 Error 和 TurnComplete 事件；第二个请求正常成功，证明 agent 正确清除了运行中的任务状态 |
