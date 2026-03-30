# `tool_harness.rs` -- 测试模块

## 测试覆盖范围
测试工具执行的基础 harness 功能，包括 local shell 工具执行与输出验证、update_plan 工具的事件发送与格式校验，以及 apply_patch 工具的文件创建和解析错误处理。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `shell_tool_executes_command_and_streams_output` | local_shell 工具执行 `/bin/echo tool harness`，验证 exit_code=0 和 stdout 内容 |
| `update_plan_tool_emits_plan_update_event` | update_plan 工具调用发出 PlanUpdate 事件，包含 explanation 和 plan steps（状态为 in_progress/pending），返回 "Plan updated" |
| `update_plan_tool_rejects_malformed_payload` | 缺少 plan 字段的 update_plan 调用不发出 PlanUpdate 事件，返回解析错误并标记 success=false |
| `apply_patch_tool_executes_and_emits_patch_events` | apply_patch 创建新文件，发出 PatchApplyBegin/PatchApplyEnd 事件且 success=true，验证文件内容正确 |
| `apply_patch_reports_parse_diagnostics` | apply_patch 解析失败时返回 "apply_patch verification failed" 和 "invalid hunk" 诊断信息 |
