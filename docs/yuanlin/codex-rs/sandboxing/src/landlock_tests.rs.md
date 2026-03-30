# `landlock_tests.rs` — 测试模块

## 测试覆盖范围

测试 Linux 沙箱命令行参数构建逻辑，覆盖旧版 Landlock 标志、代理网络标志和分离策略参数等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `legacy_landlock_flag_is_included_when_requested` | 验证 `--use-legacy-landlock` 标志仅在请求时包含 |
| `proxy_flag_is_included_when_requested` | 验证 `--allow-network-for-proxy` 标志在请求时包含 |
| `split_policy_flags_are_included` | 验证分离的策略 JSON 参数（文件系统策略、网络策略）和命令 cwd 正确传递 |
| `proxy_network_requires_managed_requirements` | 验证代理网络标志仅在启用托管网络要求时启用 |
