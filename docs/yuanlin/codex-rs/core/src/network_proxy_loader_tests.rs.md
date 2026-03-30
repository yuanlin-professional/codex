# `network_proxy_loader_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖网络代理加载器（NetworkProxyLoader）的配置合并逻辑，包括多层 TOML 配置的域名列表叠加、高优先级配置覆盖低优先级配置的匹配域名、execpolicy 网络规则对配置域名列表的叠加、Unix 套接字全局允许标志的应用，以及 NetworkProxyConstraints 的域名约束应用逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `higher_precedence_profile_network_overlays_domain_entries` | 验证高优先级配置层的域名条目叠加到低优先级层之上 |
| `higher_precedence_profile_network_overrides_matching_domain_entries` | 验证高优先级配置覆盖匹配域名的 allow/deny 决策 |
| `execpolicy_network_rules_overlay_network_lists` | 验证 execpolicy 网络规则叠加到已有的允许/拒绝域名列表 |
| `apply_network_constraints_includes_allow_all_unix_sockets_flag` | 验证 dangerously_allow_all_unix_sockets 标志正确应用到约束中 |
| `apply_network_constraints_skips_empty_domain_sides` | 验证仅有 allowed 域名时 denied 列表保持为 None |
| `apply_network_constraints_overlay_domain_entries` | 验证多层约束应用时 allowed 和 denied 域名列表正确叠加 |
