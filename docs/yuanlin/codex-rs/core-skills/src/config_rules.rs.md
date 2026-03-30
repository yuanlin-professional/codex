# `config_rules.rs` — 技能配置规则解析与禁用路径计算

## 功能概述
该文件负责从 `ConfigLayerStack` 中提取技能配置规则，并根据这些规则确定哪些技能路径应被禁用。支持通过名称（Name）或路径（Path）两种选择器来匹配技能，层级顺序保证后续配置可覆盖先前配置。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillConfigRuleSelector` | 枚举 | 技能规则选择器，支持 Name 和 Path 两种匹配方式 |
| `SkillConfigRule` | 结构体 | 单条技能配置规则，包含选择器和启用/禁用标志 |
| `SkillConfigRules` | 结构体 | 技能配置规则集合，包含有序的规则条目列表 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `skill_config_rules_from_stack` | `(config_layer_stack: &ConfigLayerStack) -> SkillConfigRules` | 从配置层级栈中提取技能配置规则，仅处理 User 和 SessionFlags 层 |
| `resolve_disabled_skill_paths` | `(skills: &[SkillMetadata], rules: &SkillConfigRules) -> HashSet<PathBuf>` | 根据规则集合计算出所有被禁用的技能路径 |
| `skill_config_rule_selector` | `(entry: &SkillConfig) -> Option<SkillConfigRuleSelector>` | 从单个技能配置条目中提取选择器，处理各种边界情况 |
| `normalize_rule_path` | `(path: &Path) -> PathBuf` | 使用 dunce 规范化路径，失败时返回原始路径 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `codex_config`, `tracing`, `dunce`
- **被引用方**: `manager.rs` 中的 `SkillsManager` 使用此模块进行技能禁用计算

## 实现备注
- 规则按层级顺序追加，同一选择器的后续规则会替换先前规则（通过 `retain` + `push` 实现）
- Name 选择器会匹配所有同名技能的路径，Path 选择器则精确匹配单个路径
