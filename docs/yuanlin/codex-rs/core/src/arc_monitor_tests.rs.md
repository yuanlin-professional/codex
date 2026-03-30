# `arc_monitor_tests.rs` — 测试模块

## 测试覆盖范围

ARC 安全监控模块，包括 ARC 监控请求构建（历史消息过滤、策略字段、元数据）、monitor_action 端到端调用验证、环境变量 URL 和 token 覆盖、遗留响应字段拒绝。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `build_arc_monitor_request_includes_relevant_history_and_null_policies` | 构建 ARC 监控请求包含相关历史（过滤环境上下文/评论阶段/旧工具调用）和空策略 |
| `monitor_action_posts_expected_arc_request` | monitor_action 发送预期的 ARC 请求并返回 ask-user 结果 |
| `monitor_action_uses_env_url_and_token_overrides` | monitor_action 使用环境变量覆盖 URL 和 token |
| `monitor_action_rejects_legacy_response_fields` | monitor_action 拒绝遗留响应字段（返回 Ok） |
