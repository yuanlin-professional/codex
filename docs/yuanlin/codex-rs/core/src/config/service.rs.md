# `service.rs` -- 配置读写服务层

## 功能概述
`ConfigService` 提供面向 app-server 协议的配置读写 API，支持通过 key path 读取和写入 `config.toml` 中的值。写入时执行完整的验证流程（schema 校验、feature requirements 校验、effective config 校验），支持版本冲突检测、Replace/Upsert 两种合并策略、以及被管理层覆盖时的通知。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ConfigService` | struct | 配置服务核心结构体，持有 codex_home、CLI overrides、loader overrides 和 cloud requirements |
| `ConfigServiceError` | enum | 服务错误类型，包含 `Write`（含错误码）、`Io`、`Json`、`Toml`、`Anyhow` 五种变体 |
| `MergeError` | enum (private) | 合并错误：`PathNotFound` 或 `Validation` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ConfigService::read` | `async fn(&self, params) -> Result<ConfigReadResponse>` | 读取有效配置，支持可选的 cwd 和层信息 |
| `ConfigService::write_value` | `async fn(&self, params) -> Result<ConfigWriteResponse>` | 写入单个配置值 |
| `ConfigService::batch_write` | `async fn(&self, params) -> Result<ConfigWriteResponse>` | 批量写入多个配置值 |
| `ConfigService::apply_edits` | `async fn(&self, ...) -> Result<ConfigWriteResponse>` | 内部方法：解析 key path、应用合并、验证、持久化 |
| `ConfigService::load_user_saved_config` | `async fn(&self) -> Result<UserSavedConfig>` | 加载用户保存的配置（不含 cwd 层） |
| `parse_key_path` | `fn(path) -> Result<Vec<String>>` | 将点号分隔的 key path 拆分为路径段 |
| `apply_merge` | `fn(root, segments, value, strategy) -> Result<bool>` | 在 TOML 值树上执行 Replace 或 Upsert 合并 |
| `toml_value_to_item` | `fn(value) -> Result<TomlItem>` | 将 `toml::Value` 转换为 `toml_edit::Item`，保留显式表结构 |
| `compute_override_metadata` | `fn(layers, effective, segments) -> Option<OverriddenMetadata>` | 计算用户写入是否被更高优先级层覆盖 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `codex_config`, `codex_utils_absolute_path`, `serde_json`, `toml`, `toml_edit`, `thiserror`, `tokio`
- **引用的模块**: `crate::config::edit`, `crate::config::managed_features`, `crate::config_loader`, `crate::path_utils`
- **被引用方**: app-server 的配置 API 端点

## 实现备注
- 写入操作仅允许目标为用户配置文件（`$CODEX_HOME/config.toml`），其他路径会被拒绝（`ConfigLayerReadonly`）。
- Upsert 策略在目标和源均为 table 时执行深度合并，否则退化为 Replace。
- 版本冲突通过比较 `expected_version` 与当前层的 version hash 实现乐观并发控制。
- `find_effective_layer` 从高优先级到低优先级遍历层，找到实际提供某个值的层来源。
