# `network_approval_tests.rs` — 测试模块

## 测试覆盖范围
验证网络审批服务的去重逻辑、会话缓存同步、审批等待机制和阻止请求记录。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `pending_approvals_are_deduped_per_host_protocol_and_port` | 相同 host+protocol+port 的请求去重 |
| `pending_approvals_do_not_dedupe_across_ports` | 不同端口的请求不去重 |
| `session_approved_hosts_preserve_protocol_and_port_scope` | 会话审批缓存保留协议和端口范围 |
| `sync_session_approved_hosts_to_replaces_existing_target_hosts` | 缓存同步替换目标的现有主机 |
| `pending_waiters_receive_owner_decision` | 等待者接收到所有者的审批决策 |
| `allow_once_and_allow_for_session_both_allow_network` | AllowOnce 和 AllowForSession 都映射为 NetworkDecision::Allow |
| `only_never_policy_disables_network_approval_flow` | 仅 Never 策略禁用网络审批流程 |
| `record_blocked_request_sets_policy_outcome_for_owner_call` | 阻止请求记录为策略拒绝结果 |
| `blocked_request_policy_does_not_override_user_denial_outcome` | 用户拒绝不被策略拒绝覆盖 |
| `record_blocked_request_ignores_ambiguous_unattributed_blocked_requests` | 多个活跃调用时忽略无法归属的阻止请求 |
