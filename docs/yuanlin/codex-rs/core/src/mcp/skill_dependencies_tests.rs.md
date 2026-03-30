# `skill_dependencies_tests.rs` — 测试模块

## 测试覆盖范围

针对 `skill_dependencies.rs` 中的 `collect_missing_mcp_dependencies` 函数进行测试，验证规范化键匹配和去重逻辑在不同场景下的正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `collect_missing_respects_canonical_installed_key` | 验证已安装的 MCP 服务器即使名称不同（别名），只要 URL 匹配，依赖即被视为已安装 |
| `collect_missing_dedupes_by_canonical_key_but_preserves_original_name` | 验证同一 URL 的多个依赖声明（不同别名）只返回第一个，不会产生重复安装 |
