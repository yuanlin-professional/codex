# `review.rs` — 测试模块

## 测试覆盖范围
测试 `review/start` JSON-RPC 方法，验证代码审查 turn 的执行和 code_review item 发送、
命令执行审批 item ID 匹配、空分支/提交SHA/自定义指令的拒绝，以及分离交付模式下返回新线程 ID。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `review_start_runs_review_turn_and_emits_code_review_item` | 审查执行并发送 code_review item |
| `review_start_exec_approval_item_id_matches_command_execution_item` | 审批 item ID 与命令执行 item 匹配 |
| `review_start_rejects_empty_base_branch` | 空的 base branch 被拒绝 |
| `review_start_with_detached_delivery_returns_new_thread_id` | 分离交付模式返回新线程 ID |
| `review_start_rejects_empty_commit_sha` | 空的 commit SHA 被拒绝 |
| `review_start_rejects_empty_custom_instructions` | 空的自定义指令被拒绝 |
