# `marketplace_tests.rs` -- 测试模块

## 测试覆盖范围
全面测试 `marketplace.rs` 中的市场发现、加载、插件解析和策略执行逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `resolve_marketplace_plugin_finds_repo_marketplace_plugin` | 验证从仓库市场中正确解析本地插件 |
| `resolve_marketplace_plugin_reports_missing_plugin` | 验证请求不存在的插件时返回 PluginNotFound 错误 |
| `list_marketplaces_returns_home_and_repo_marketplaces` | 验证同时返回主目录和仓库的市场 |
| `list_marketplaces_keeps_distinct_entries_for_same_name` | 验证同名市场保持独立条目 |
| `list_marketplaces_dedupes_multiple_roots_in_same_repo` | 验证同仓库多根目录去重 |
| `list_marketplaces_reads_marketplace_display_name` | 验证读取市场的 displayName 界面字段 |
| `list_marketplaces_skips_marketplaces_that_fail_to_load` | 验证跳过加载失败的市场 |
| `list_marketplaces_reports_marketplace_load_errors` | 验证报告市场加载错误 |
| `list_marketplaces_resolves_plugin_interface_paths_to_absolute` | 验证插件界面资产路径解析为绝对路径，并正确处理产品限制和分类 |
| `list_marketplaces_ignores_legacy_top_level_policy_fields` | 验证忽略遗留的顶级策略字段（installPolicy/authPolicy） |
| `list_marketplaces_ignores_plugin_interface_assets_without_dot_slash` | 验证不以 `./` 开头的资产路径被忽略 |
| `resolve_marketplace_plugin_rejects_non_relative_local_paths` | 验证拒绝非相对路径（如 `../`） |
| `resolve_marketplace_plugin_uses_first_duplicate_entry` | 验证重复插件使用第一个条目 |
| `resolve_marketplace_plugin_rejects_disallowed_product` | 验证拒绝不匹配的产品限制 |
| `resolve_marketplace_plugin_allows_missing_products_field` | 验证缺少 products 字段时允许所有产品 |
| `resolve_marketplace_plugin_rejects_explicit_empty_products` | 验证显式空 products 列表拒绝所有产品 |
