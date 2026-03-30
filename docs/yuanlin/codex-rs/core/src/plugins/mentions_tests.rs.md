# `mentions_tests.rs` -- 测试模块

## 测试覆盖范围
测试 `mentions.rs` 中的应用 ID 收集和插件提及提取功能。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `collect_explicit_app_ids_from_linked_text_mentions` | 验证从链接文本 `[$calendar](app://calendar)` 中提取应用 ID |
| `collect_explicit_app_ids_dedupes_structured_and_linked_mentions` | 验证结构化提及和链接提及去重 |
| `collect_explicit_app_ids_ignores_non_app_paths` | 验证忽略非 `app://` 协议的路径（mcp://、skill:// 等） |
| `collect_explicit_plugin_mentions_from_structured_paths` | 验证从结构化 `UserInput::Mention` 中提取插件提及 |
| `collect_explicit_plugin_mentions_from_linked_text_mentions` | 验证从 `[@sample](plugin://sample@test)` 链接文本中提取插件提及 |
| `collect_explicit_plugin_mentions_dedupes_structured_and_linked_mentions` | 验证插件提及的结构化和文本方式去重 |
| `collect_explicit_plugin_mentions_ignores_non_plugin_paths` | 验证忽略非 `plugin://` 协议的路径 |
| `collect_explicit_plugin_mentions_ignores_dollar_linked_plugin_mentions` | 验证使用 `$` 符号的插件链接（`[$sample](plugin://...)`）被忽略，必须使用 `@` 符号 |
