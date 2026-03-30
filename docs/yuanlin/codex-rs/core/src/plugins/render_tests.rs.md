# `render_tests.rs` -- 测试模块

## 测试覆盖范围
测试 `render.rs` 中插件提示词渲染函数的正确性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `render_plugins_section_returns_none_for_empty_plugins` | 验证空插件列表返回 None |
| `render_plugins_section_includes_descriptions_and_skill_naming_guidance` | 验证渲染结果包含插件描述、技能命名指南和使用说明，并被正确的 XML 标签包裹 |
