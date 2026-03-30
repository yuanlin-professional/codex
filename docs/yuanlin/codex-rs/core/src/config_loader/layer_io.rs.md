# `layer_io.rs` -- 配置层 I/O 加载

## 功能概述
负责从文件系统和平台特定位置（如 macOS managed preferences）加载配置层的原始 TOML 数据。提供异步的配置文件读取、TOML 解析和错误处理，以及平台相关的默认路径解析（Unix 使用 `/etc/codex/managed_config.toml`，Windows 使用 codex_home 下的文件）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MangedConfigFromFile` | struct | 从文件加载的 managed config，包含解析后的 TOML 值和文件路径 |
| `ManagedConfigFromMdm` | struct | 从 MDM（设备管理）加载的 managed config，包含 TOML 值和原始 TOML 字符串 |
| `LoadedConfigLayers` | struct | 加载结果，包含可选的文件 managed config 和 MDM managed config |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_config_layers_internal` | `async fn(codex_home, overrides) -> Result<LoadedConfigLayers>` | 主入口：加载文件和 MDM 两个 managed config 来源 |
| `read_config_from_path` | `async fn(path, log_missing_as_info) -> Result<Option<TomlValue>>` | 异步读取并解析 TOML 文件，文件不存在时返回 None |
| `managed_config_default_path` | `fn(codex_home) -> PathBuf` | 返回平台相关的默认 managed config 路径 |

## 依赖关系
- **引用的 crate**: `codex_config`, `codex_utils_absolute_path`, `tokio`, `toml`
- **引用的模块**: `super::LoaderOverrides`, `super::macos`（macOS 平台）
- **被引用方**: `config_loader/mod.rs` 中的 `load_config_layers_state`

## 实现备注
- `read_config_from_path` 对文件不存在的情况区分 info/debug 日志级别，便于区分预期缺失（如 managed_config）和非预期缺失（如 user config）。
- macOS 平台额外加载 `ManagedAdminConfigLayer` 并通过 `map_managed_admin_layer` 转换为统一的 `ManagedConfigFromMdm` 结构。
