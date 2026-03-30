# `mod.rs` -- 配置加载器主模块

## 功能概述
配置加载器是 Codex 配置系统的核心，负责从多个来源（云端需求、MDM managed preferences、系统 requirements.toml、系统 config.toml、用户 config.toml、项目 .codex/ 目录、CLI 覆盖、legacy managed_config.toml）加载配置层并构建 `ConfigLayerStack`。实现了完整的配置优先级体系、项目信任上下文管理、相对路径解析、以及 legacy 配置格式的向后兼容。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ProjectTrustContext` | struct (private) | 项目信任上下文，包含项目根路径、repo 根路径、信任映射和用户配置文件路径 |
| `ProjectTrustDecision` | struct (private) | 信任决策结果，包含信任级别和用于持久化的 trust key |
| `LegacyManagedConfigToml` | struct (private) | 旧版 managed_config.toml 的反序列化结构，仅支持 `approval_policy` 和 `sandbox_mode` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_config_layers_state` | `async fn(codex_home, cwd, cli_overrides, overrides, cloud_requirements) -> Result<ConfigLayerStack>` | 主入口：加载所有配置层和需求约束，构建最终的配置层栈 |
| `load_config_toml_for_required_layer` | `async fn(config_toml, create_entry) -> Result<ConfigLayerEntry>` | 加载单个 config.toml 文件为配置层条目 |
| `load_requirements_toml` | `async fn(target, file) -> Result<()>` | 加载系统 requirements.toml 并合并到需求集合 |
| `load_project_layers` | `async fn(cwd, project_root, trust_context, codex_home) -> Result<Vec<ConfigLayerEntry>>` | 加载项目目录链中的所有 .codex/ 配置层 |
| `resolve_relative_paths_in_config_toml` | `fn(value, base_dir) -> Result<TomlValue>` | 解析 config TOML 中的相对路径为绝对路径 |
| `find_project_root` | `async fn(cwd, markers) -> Result<AbsolutePathBuf>` | 从 cwd 向上搜索项目根标记文件 |
| `project_trust_context` | `async fn(config, cwd, markers, codex_home, user_config_file) -> Result<ProjectTrustContext>` | 构建项目信任上下文 |
| `first_layer_config_error` | `async fn(layers) -> Option<ConfigError>` | 检查配置层中的第一个配置错误 |
| `system_config_toml_file` | `fn() -> Result<AbsolutePathBuf>` | 返回平台相关的系统配置文件路径 |

## 依赖关系
- **引用的 crate**: `codex_config`, `codex_git_utils`, `codex_protocol`, `codex_utils_absolute_path`, `dunce`, `toml`
- **子模块**: `layer_io`, `macos`（macOS 平台）, `tests`
- **re-export**: 从 `codex_config` 重导出大量类型（`ConfigLayerStack`, `ConfigRequirementsToml`, `LoaderOverrides` 等）
- **被引用方**: `config/mod.rs`、`config/service.rs`

## 实现备注
- 配置加载顺序：cloud requirements > MDM requirements > system requirements > [system config | user config | project configs | CLI overrides | legacy managed configs]。
- 需求约束遵循"先到先得"原则：较高优先级来源定义的约束不会被较低优先级来源覆盖。
- 项目配置层按目录深度排序（从项目根到 cwd），越靠近 cwd 优先级越高。
- 不受信任的项目配置层被加载但标记为 `disabled`（保留内容但不参与合并），支持后续信任后启用。
- `resolve_relative_paths_in_config_toml` 通过 serialize/deserialize round-trip 解析 `AbsolutePathBuf` 字段中的相对路径，然后用 `copy_shape_from_original` 保留原始 TOML 中的未识别字段。
- `LegacyManagedConfigToml` 的 `From` 实现将旧版 `managed_config.toml` 中的 `sandbox_mode` 映射为 `allowed_sandbox_modes`，始终包含 `ReadOnly` 以保证基本功能。
