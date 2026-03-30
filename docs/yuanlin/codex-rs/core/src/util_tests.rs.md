# `util_tests.rs` — 测试模块

## 测试覆盖范围

工具函数（util）模块，包括 feedback_tags 宏编译验证、反馈请求标签发射与 Sentry 字段记录、认证恢复标签发射、认证环境遥测字段保留、线程名称规范化、恢复命令生成（含引号处理）。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `feedback_tags_macro_compiles` | feedback_tags 宏编译验证 |
| `emit_feedback_request_tags_records_sentry_feedback_fields` | 发射反馈请求标签记录 Sentry 反馈字段 |
| `emit_feedback_auth_recovery_tags_preserves_401_specific_fields` | 发射认证恢复标签保留 401 特定字段 |
| `emit_feedback_auth_recovery_tags_clears_stale_401_fields` | 发射认证恢复标签清除过期 401 字段 |
| `emit_feedback_request_tags_preserves_latest_auth_fields_after_unauthorized` | 未授权后发射反馈请求标签保留最新认证字段 |
| `emit_feedback_request_tags_preserves_auth_env_fields_for_legacy_emitters` | 遗留发射器保留认证环境字段 |
| `normalize_thread_name_trims_and_rejects_empty` | 线程名称规范化修剪空白并拒绝空名称 |
| `resume_command_prefers_name_over_id` | 恢复命令优先使用名称而非 ID |
| `resume_command_with_only_id` | 仅有 ID 时的恢复命令生成 |
| `resume_command_with_no_name_or_id` | 无名称和 ID 时恢复命令返回 None |
| `resume_command_quotes_thread_name_when_needed` | 需要时对线程名称加引号（破折号开头/空格/单引号） |
