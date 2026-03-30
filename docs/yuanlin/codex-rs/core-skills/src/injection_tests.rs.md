# `injection_tests.rs` — 测试模块

## 测试覆盖范围
该文件测试技能提及检测和显式技能提及收集逻辑，包括文本中 `$skillname` 的边界匹配、`[$name](path)` 链接语法解析、环境变量排除、歧义名称处理、禁用技能过滤、连接器冲突处理以及结构化输入优先级等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `text_mentions_skill_requires_exact_boundary` | `$skillname` 需要精确单词边界 |
| `text_mentions_skill_handles_end_boundary_and_near_misses` | 处理末尾边界和近似匹配 |
| `text_mentions_skill_handles_many_dollars_without_looping` | 大量 `$` 符号不会导致无限循环 |
| `extract_tool_mentions_handles_plain_and_linked_mentions` | 处理普通和链接式提及 |
| `extract_tool_mentions_skips_common_env_vars` | 跳过常见环境变量如 $PATH, $HOME |
| `collect_explicit_skill_mentions_text_respects_skill_order` | 文本扫描保持技能原始顺序 |
| `collect_explicit_skill_mentions_prioritizes_structured_inputs` | 结构化输入优先于文本提及 |
| `collect_explicit_skill_mentions_skips_ambiguous_name` | 跳过歧义的同名技能 |
| `collect_explicit_skill_mentions_prefers_linked_path_over_name` | 链接路径优先于名称匹配 |
| `collect_explicit_skill_mentions_skips_plain_name_when_connector_matches` | 连接器冲突时跳过纯名称提及 |
