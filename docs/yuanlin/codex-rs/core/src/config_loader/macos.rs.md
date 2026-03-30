# `macos.rs` -- macOS Managed Preferences 加载

## 功能概述
在 macOS 平台上通过 Core Foundation API 读取设备管理（MDM）配置的 managed preferences。支持加载 base64 编码的配置 TOML（`config_toml_base64`）和需求 TOML（`requirements_toml_base64`），两者均存储在 `com.openai.codex` 应用域下。提供覆盖机制以支持测试注入。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ManagedAdminConfigLayer` | struct | 从 MDM 加载的管理员配置层，包含解析后的 `TomlValue` 和原始 TOML 字符串 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_managed_admin_config_layer` | `async fn(override_base64) -> Result<Option<ManagedAdminConfigLayer>>` | 加载 MDM 配置层，优先使用 override 参数 |
| `load_managed_admin_requirements_toml` | `async fn(target, override_base64) -> Result<()>` | 加载 MDM 需求配置并合并到目标 |
| `load_managed_preference` | `fn(key_name) -> Result<Option<String>>` | 通过 `CFPreferencesCopyAppValue` 读取 managed preference 值 |
| `parse_managed_config_base64` | `fn(encoded) -> Result<ManagedAdminConfigLayer>` | 解码 base64 并解析为 TOML 配置层 |
| `parse_managed_requirements_base64` | `fn(encoded) -> Result<ConfigRequirementsToml>` | 解码 base64 并解析为需求 TOML |
| `managed_preferences_requirements_source` | `fn() -> RequirementSource` | 返回 MDM managed preferences 的需求来源标识 |

## 依赖关系
- **引用的 crate**: `core_foundation`, `base64`, `tokio`, `toml`
- **引用的模块**: `super::{ConfigRequirementsToml, ConfigRequirementsWithSources, RequirementSource}`
- **被引用方**: `config_loader/layer_io.rs`、`config_loader/mod.rs`

## 实现备注
- `load_managed_preference` 使用 `unsafe` 调用 Core Foundation 的 `CFPreferencesCopyAppValue`，通过 `CFString::wrap_under_create_rule` 管理内存。
- 加载操作通过 `task::spawn_blocking` 在阻塞线程上执行，避免阻塞 tokio 运行时。
- 空 base64 override 被视为"无配置"（返回 `Ok(None)`），非空则直接解析而不查询系统。
