# `network_proxy_spec_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `network_proxy_spec.rs` 中网络代理规格配置的合并与约束验证逻辑，包括域名白名单/黑名单在不同 sandbox 模式下的合并行为、managed_allowed_domains_only 模式、以及审计元数据传递。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `build_state_with_audit_metadata_threads_metadata_to_state` | 验证审计元数据正确传递到代理状态 |
| `requirements_allowed_domains_are_a_baseline_for_user_allowlist` | 验证管理员白名单作为用户白名单的基线 |
| `requirements_allowed_domains_do_not_override_user_denies_for_same_pattern` | 验证管理员白名单不覆盖用户的同域名拒绝 |
| `requirements_allowlist_expansion_keeps_user_entries_mutable` | 验证白名单扩展模式下用户条目仍可修改 |
| `danger_full_access_keeps_managed_allowlist_and_denylist_fixed` | 验证 FullAccess 模式下管理员列表固定不变 |
| `managed_allowed_domains_only_disables_default_mode_allowlist_expansion` | 验证 managed-only 模式禁用白名单扩展 |
| `managed_allowed_domains_only_ignores_user_allowlist_and_hard_denies_misses` | 验证 managed-only 模式忽略用户白名单且硬拒绝 |
| `managed_allowed_domains_only_without_managed_allowlist_blocks_all_user_domains` | 验证无管理员白名单时 managed-only 模式阻止所有用户域名 |
| `deny_only_requirements_do_not_create_allow_constraints_in_full_access` | 验证仅拒绝需求不创建白名单约束 |
| `allow_only_requirements_do_not_create_deny_constraints_in_full_access` | 验证仅允许需求不创建黑名单约束 |
| `requirements_denied_domains_are_a_baseline_for_default_mode` | 验证管理员黑名单作为默认模式的基线 |
| `requirements_denylist_expansion_keeps_user_entries_mutable` | 验证黑名单扩展模式下用户条目仍可修改 |
