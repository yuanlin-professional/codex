# `config_api.rs` — 配置读写 API 与运行时特性开关管理

## 功能概述

本模块实现了 `ConfigApi` 结构体，提供配置的读取、写入、批量写入、需求约束读取以及实验性特性开关的运行时切换等功能。它封装了 `codex_core::config::ConfigService`，并在其基础上添加了 CLI 覆盖值管理、运行时特性开关（如 apps、plugins、tool_search 等）的优先级处理、以及云端配置需求到 app-server 协议类型的映射转换。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ConfigApi` | 结构体 | 配置 API 服务，持有 codex_home 路径、CLI 覆盖值、运行时特性开关等 |
| `UserConfigReloader` | trait | 用户配置重载接口，`ThreadManager` 实现了此 trait |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_latest_config` | `async fn(&self, fallback_cwd) -> Result<Config, ...>` | 加载最新配置并应用运行时特性开关 |
| `read` | `async fn(&self, params) -> Result<ConfigReadResponse, ...>` | 读取配置（含特性开关状态增强） |
| `config_requirements_read` | `async fn(&self) -> Result<ConfigRequirementsReadResponse, ...>` | 读取配置需求约束 |
| `write_value` | `async fn(&self, params) -> Result<ConfigWriteResponse, ...>` | 写入单个配置值 |
| `batch_write` | `async fn(&self, params) -> Result<ConfigWriteResponse, ...>` | 批量写入配置值，可选触发用户配置重载 |
| `set_experimental_feature_enablement` | `async fn(&self, params) -> Result<...>` | 设置运行时实验性特性开关 |
| `apply_runtime_feature_enablement` | `fn(config, enablement)` | 将运行时特性开关应用到 Config 对象（受保护特性除外） |
| `protected_feature_keys` | `fn(config_layer_stack) -> BTreeSet<String>` | 获取受保护的特性键集合 |

## 依赖关系

- **引用的 crate**: `codex_core::config`（`ConfigService`、`ConfigBuilder`）、`codex_features`、`codex_app_server_protocol`
- **被引用方**: `message_processor.rs`（处理 `config/*` 请求时调用）

## 实现备注

- 特性开关优先级从高到低为：云端需求 > CLI 覆盖 > 运行时开关 > 配置文件。
- `protected_feature_keys` 合并了配置层栈中的已有特性键和需求约束中的特性键。
- 配置写入时会检测插件启用/禁用变更并发送分析事件。
- 包含详尽的单元测试，验证特性开关优先级、需求映射、批量写入后重载等场景。
