# `policy.rs` — 策略核心：规则匹配与命令评估

## 功能概述

`Policy` 是执行策略的核心结构，管理按程序名索引的规则集合、网络规则列表和宿主可执行文件映射。提供了丰富的命令检查 API：单命令检查、多命令批量检查、带自定义选项的检查。支持启发式回退（当无规则匹配时使用回退函数决定策略）、宿主可执行文件解析（将绝对路径映射为基名规则）、策略叠加合并、动态添加规则等高级功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Policy` | 结构体 | 策略容器，包含 rules_by_program、network_rules 和 host_executables_by_name |
| `MatchOptions` | 结构体 | 匹配选项，控制是否启用宿主可执行文件解析 |
| `Evaluation` | 结构体 | 评估结果，包含最终决策和所有匹配的规则列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(rules_by_program) -> Self` | 从规则 MultiMap 构造策略 |
| `from_parts` | `(rules, network_rules, host_executables) -> Self` | 从完整组件构造策略 |
| `empty` | `() -> Self` | 创建空策略 |
| `check` | `(&self, cmd, heuristics_fallback) -> Evaluation` | 检查命令并返回评估结果 |
| `check_multiple` | `(&self, commands, heuristics_fallback) -> Evaluation` | 批量检查多个命令 |
| `matches_for_command` | `(&self, cmd, heuristics_fallback) -> Vec<RuleMatch>` | 返回命令的所有匹配规则 |
| `add_prefix_rule` | `(&mut self, prefix, decision) -> Result<()>` | 动态添加前缀规则 |
| `add_network_rule` | `(&mut self, host, protocol, decision, justification) -> Result<()>` | 动态添加网络规则 |
| `merge_overlay` | `(&self, overlay) -> Policy` | 合并叠加层策略 |
| `compiled_network_domains` | `(&self) -> (Vec<String>, Vec<String>)` | 编译网络规则为允许/拒绝域名列表 |
| `get_allowed_prefixes` | `(&self) -> Vec<Vec<String>>` | 提取所有允许的前缀规则 |

## 依赖关系

- **引用的 crate**: `multimap`、`serde`、`codex_utils_absolute_path`
- **被引用方**: `parser.rs`、`execpolicycheck.rs`、`amend.rs`、runtime 层

## 实现备注

- 匹配优先级：精确程序名匹配 > 宿主可执行文件解析匹配 > 启发式回退。
- 当启用 `resolve_host_executables` 时，绝对路径的命令会被解析为基名，然后在宿主可执行文件白名单中验证路径合法性。
- `Evaluation::from_matches` 取所有匹配规则中决策的最大值（最严格者胜出）。
- 网络域名编译采用"后者覆盖前者"的语义：后出现的规则可以覆盖先前的允许/拒绝状态。
