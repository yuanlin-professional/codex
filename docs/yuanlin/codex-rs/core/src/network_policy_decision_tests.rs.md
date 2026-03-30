# `network_policy_decision_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖网络策略决策（NetworkPolicyDecision）的处理逻辑，包括审批上下文（NetworkApprovalContext）的生成条件、不同网络协议（HTTP/HTTPS/SOCKS5）的映射、代理协议别名的反序列化、execpolicy 网络规则修正（amendment）的构建，以及被拒绝网络请求的消息格式。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `network_approval_context_requires_ask_from_decider` | 验证 Deny 决策不生成审批上下文（仅 Ask 决策才需要审批） |
| `network_approval_context_maps_http_https_and_socks_protocols` | 验证 HTTP、HTTPS、SOCKS5 TCP 和 SOCKS5 UDP 协议正确映射到审批上下文 |
| `network_policy_decision_payload_deserializes_proxy_protocol_aliases` | 验证代理协议别名（https_connect、http-connect）正确反序列化为 HTTPS 协议 |
| `execpolicy_network_rule_amendment_maps_protocol_action_and_justification` | 验证网络规则修正正确映射协议、操作和理由字段 |
| `denied_network_policy_message_requires_deny_decision` | 验证 Ask 决策不生成拒绝消息 |
| `denied_network_policy_message_for_denylist_block_is_explicit` | 验证 Deny 决策（来自基线策略）生成明确的域名拒绝消息 |
