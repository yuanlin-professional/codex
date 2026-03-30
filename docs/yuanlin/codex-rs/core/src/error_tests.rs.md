# `error_tests.rs` — 测试模块

## 测试覆盖范围

错误处理与格式化，包括使用量限制错误（不同计划类型的消息格式化）、沙箱拒绝错误消息生成、服务器过载错误映射、意外响应错误格式化（Cloudflare 拦截/长 body 截断/身份认证详情）、响应流失败事件、速率限制时间戳格式化。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `usage_limit_reached_error_formats_plus_plan` | Plus 计划的使用量限制错误格式化 |
| `server_overloaded_maps_to_protocol` | 服务器过载映射到协议错误信息 |
| `sandbox_denied_uses_aggregated_output_when_stderr_empty` | stderr 为空时沙箱拒绝使用聚合输出 |
| `sandbox_denied_reports_both_streams_when_available` | stdout 和 stderr 都可用时沙箱拒绝报告两者 |
| `sandbox_denied_reports_stdout_when_no_stderr` | 无 stderr 时沙箱拒绝报告 stdout |
| `to_error_event_handles_response_stream_failed` | 处理响应流失败的错误事件 |
| `sandbox_denied_reports_exit_code_when_no_output_available` | 无输出时沙箱拒绝报告退出码 |
| `usage_limit_reached_error_formats_free_plan` | Free 计划的使用量限制错误格式化 |
| `usage_limit_reached_error_formats_go_plan` | Go 计划的使用量限制错误格式化 |
| `usage_limit_reached_error_formats_default_when_none` | 无计划类型时使用默认错误格式化 |
| `usage_limit_reached_error_formats_team_plan` | Team 计划的使用量限制错误格式化（含重试时间） |
| `usage_limit_reached_error_formats_business_plan_without_reset` | Business 计划无重置时间的错误格式化 |
| `usage_limit_reached_error_formats_self_serve_business_usage_based_plan` | 自助 Business 用量计费计划的错误格式化 |
| `usage_limit_reached_error_formats_enterprise_cbp_usage_based_plan` | Enterprise CBP 用量计费计划的错误格式化 |
| `usage_limit_reached_error_formats_default_for_other_plans` | 其他计划类型使用默认错误格式化 |
| `usage_limit_reached_error_formats_pro_plan_with_reset` | Pro 计划含重置时间的错误格式化 |
| `usage_limit_reached_error_hides_upsell_for_non_codex_limit_name` | 非 Codex 限制名称时隐藏升级推销 |
| `usage_limit_reached_includes_minutes_when_available` | 可用时在重试时间中包含分钟数 |
| `unexpected_status_cloudflare_html_is_simplified` | Cloudflare HTML 错误被简化显示 |
| `unexpected_status_non_html_is_unchanged` | 非 HTML 错误保持原样 |
| `unexpected_status_prefers_error_message_when_present` | 存在时优先使用 error.message 字段 |
| `unexpected_status_truncates_long_body_with_ellipsis` | 长 body 被截断并添加省略号 |
| `unexpected_status_includes_cf_ray_and_request_id` | 包含 cf-ray 和 request_id |
| `unexpected_status_includes_identity_auth_details` | 包含身份认证错误详情 |
| `usage_limit_reached_includes_hours_and_minutes` | 重试时间包含小时和分钟 |
| `usage_limit_reached_includes_days_hours_minutes` | 重试时间包含天/小时/分钟 |
| `usage_limit_reached_less_than_minute` | 重试时间不足一分钟的格式化 |
| `usage_limit_reached_with_promo_message` | 含推广消息的使用量限制错误格式化 |
