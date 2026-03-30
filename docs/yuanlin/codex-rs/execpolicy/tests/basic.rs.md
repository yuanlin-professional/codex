# `basic.rs` — 测试模块

## 测试覆盖范围

execpolicy crate 的综合集成测试，覆盖策略解析、规则匹配、决策评估、多文件策略合并、宿主可执行文件解析、网络规则编译等核心功能。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `append_allow_prefix_rule_dedupes_existing_rule` | 验证重复追加相同前缀规则会自动去重 |
| `network_rules_compile_into_domain_lists` | 验证网络规则正确编译为允许/拒绝域名列表 |
| `network_rule_rejects_wildcard_hosts` | 验证通配符主机名被拒绝 |
| `basic_match` | 验证基本的前缀规则匹配 |
| `justification_is_attached_to_forbidden_matches` | 验证禁止规则的理由说明被正确附带 |
| `justification_can_be_used_with_allow_decision` | 验证允许规则也可以附带理由说明 |
| `justification_cannot_be_empty` | 验证空白理由说明被拒绝 |
| `add_prefix_rule_extends_policy` | 验证动态添加前缀规则到策略 |
| `add_prefix_rule_rejects_empty_prefix` | 验证空前缀被拒绝 |
| `parses_multiple_policy_files` | 验证多个策略文件可以被依次解析并合并 |
| `only_first_token_alias_expands_to_multiple_rules` | 验证第一个 token 中的替代列表展开为多条规则 |
| `tail_aliases_are_not_cartesian_expanded` | 验证后续 token 的替代列表不做笛卡尔积展开 |
| `match_and_not_match_examples_are_enforced` | 验证策略中的正例/反例在解析时被强制验证 |
| `strictest_decision_wins_across_matches` | 验证多条规则匹配时取最严格的决策 |
| `strictest_decision_across_multiple_commands` | 验证跨命令批量检查时取最严格决策 |
| `heuristics_match_is_returned_when_no_policy_matches` | 验证无规则匹配时使用启发式回退 |
| `parses_host_executable_paths` | 验证宿主可执行文件路径解析和去重 |
| `host_executable_rejects_non_absolute_path` | 验证非绝对路径被拒绝 |
| `host_executable_rejects_name_with_path_separator` | 验证含路径分隔符的名称被拒绝 |
| `host_executable_rejects_path_with_wrong_basename` | 验证基名不匹配的路径被拒绝 |
| `host_executable_last_definition_wins` | 验证后定义的宿主可执行文件覆盖先前定义 |
| `host_executable_resolution_uses_basename_rule_when_allowed` | 验证绝对路径命令通过宿主可执行文件白名单解析为基名规则 |
| `prefix_rule_examples_honor_host_executable_resolution` | 验证正例/反例中的绝对路径也遵循宿主可执行文件解析 |
| `host_executable_resolution_respects_explicit_empty_allowlist` | 验证空白名单阻止绝对路径解析 |
| `host_executable_resolution_ignores_path_not_in_allowlist` | 验证不在白名单中的路径不被解析 |
| `host_executable_resolution_falls_back_without_mapping` | 验证无宿主可执行文件映射时回退到基名匹配 |
| `host_executable_resolution_does_not_override_exact_match` | 验证精确路径规则优先于宿主可执行文件解析 |
