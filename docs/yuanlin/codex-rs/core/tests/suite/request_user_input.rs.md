# `request_user_input.rs` -- 测试模块

## 测试覆盖范围
测试 request_user_input 工具在不同协作模式（Plan、Execute、Default、PairProgramming）下的行为，包括正常的请求-响应往返和不可用时的拒绝逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `request_user_input_round_trip_resolves_pending` | Plan 模式下，request_user_input 发出问题后等待用户回答，回答通过 UserInputAnswer 提交后 turn 正常完成 |
| `request_user_input_rejected_in_execute_mode_alias` | Execute 模式下 request_user_input 被拒绝，返回 "unavailable in Execute mode" |
| `request_user_input_rejected_in_default_mode_by_default` | Default 模式下（未启用 feature）request_user_input 被拒绝 |
| `request_user_input_round_trip_in_default_mode_with_feature` | Default 模式下启用 DefaultModeRequestUserInput feature 后可正常使用 |
| `request_user_input_rejected_in_pair_mode_alias` | Pair Programming 模式下 request_user_input 被拒绝 |
