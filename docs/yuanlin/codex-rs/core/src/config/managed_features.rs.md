# `managed_features.rs` -- 受管理的功能特性约束引擎

## 功能概述
`ManagedFeatures` 结构体在 `Features` 之上封装了约束层，确保管理员通过 `FeatureRequirementsToml` 定义的功能钉住（pin）规则在构造和修改时始终得到满足。所有特性变更都经过规范化（依赖项自动调整）和验证（不违反约束）两个步骤。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ManagedFeatures` | struct | 包装 `ConstrainedWithSource<Features>` 和 `pinned_features`，提供受约束的特性管理 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ManagedFeatures::from_configured` | `fn(configured_features, feature_requirements) -> Result<Self>` | 从配置的特性和需求约束构造实例，验证钉住特性的一致性 |
| `ManagedFeatures::set` | `fn(&mut self, candidate) -> ConstraintResult<()>` | 设置新的特性集，自动规范化并验证约束 |
| `ManagedFeatures::set_enabled` | `fn(&mut self, feature, enabled) -> ConstraintResult<()>` | 启用/禁用单个特性 |
| `normalize_candidate` | `fn(candidate, pinned_features) -> Features` | 将候选特性集按钉住规则调整，并处理依赖项 |
| `validate_pinned_features` | `fn(normalized, pinned, source) -> Result<()>` | 验证规范化后的特性集是否满足所有钉住约束 |
| `parse_feature_requirements` | `fn(requirements, source) -> Result<BTreeMap<Feature, bool>>` | 解析特性需求 TOML 为特性钉住映射 |
| `validate_explicit_feature_settings_in_config_toml` | `fn(cfg, feature_requirements) -> Result<()>` | 验证 config.toml 中的显式特性设置不与需求冲突 |
| `validate_feature_requirements_in_config_toml` | `fn(cfg, feature_requirements) -> Result<()>` | 验证每个 profile 的特性配置满足需求约束 |

## 依赖关系
- **引用的 crate**: `codex_config`, `codex_features`
- **引用的模块**: `crate::config::ConfigToml`, `crate::config::profile::ConfigProfile`
- **被引用方**: `config/mod.rs`（构建 `Config` 时验证特性约束）, `config/service.rs`（写入配置时验证约束）

## 实现备注
- `normalize_candidate` 先应用钉住规则，再调用 `normalize_dependencies()` 处理特性间的依赖关系（例如启用某特性可能自动启用其前置特性）。
- `explicit_feature_settings_in_config` 同时检查根级 `features`、遗留字段（如 `experimental_use_unified_exec_tool`）以及所有 profile 中的特性设置。
- 测试中通过 `impl From<Features> for ManagedFeatures` 提供无约束的构造方式。
