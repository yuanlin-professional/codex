# `profile.rs` -- 配置 Profile 定义

## 功能概述
定义了 `ConfigProfile` 结构体，表示 `config.toml` 中的一组可命名、可切换的配置选项集合。用户可以在 `[profiles.<name>]` 下定义多个 profile，每个 profile 包含模型、审批策略、沙箱模式、推理参数等常用配置项。该结构体同时实现了到 `codex_app_server_protocol::Profile` 的转换。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ConfigProfile` | struct | 配置 profile，包含 `model`、`service_tier`、`model_provider`、`approval_policy`、`sandbox_mode`、`model_reasoning_effort`、`personality`、`features` 等 30+ 个可选字段 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `From<ConfigProfile> for codex_app_server_protocol::Profile` | trait impl | 将配置 profile 转换为 app-server 协议的 Profile 类型 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_features`, `codex_utils_absolute_path`, `schemars`, `serde`
- **引用的模块**: `crate::config::types`, `crate::config::ToolsToml`
- **被引用方**: `config/mod.rs`（profile 选择与配置合并）, `config/managed_features.rs`（特性约束验证）

## 实现备注
- 所有字段均为 `Option<T>`，未设置的字段从上层配置继承。
- `features` 字段使用 `crate::config::schema::features_schema` 自定义 JSON Schema 生成，确保只允许已知的 feature key。
- 部分字段（如 `experimental_instructions_file`）已弃用并标记 `#[schemars(skip)]`。
