# `state.rs` — 配置层栈状态管理

## 功能概述

实现配置层栈（`ConfigLayerStack`）和配置层条目（`ConfigLayerEntry`），管理 Codex 多层配置系统的完整生命周期。配置层按优先级从低到高排列（System < User < Project < SessionFlags），支持层的合并、用户层替换/插入、字段来源追踪、以及有效配置的计算。同时维护与配置层分离的需求约束（requirements）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ConfigLayerEntry` | 结构体 | 单个配置层条目：包含来源名称、TOML 配置值、原始 TOML 文本、版本指纹和禁用原因 |
| `ConfigLayerStack` | 结构体 | 配置层栈：有序存储所有配置层，维护用户层索引和需求约束 |
| `ConfigLayerStackOrdering` | 枚举 | 层遍历顺序：`LowestPrecedenceFirst` / `HighestPrecedenceFirst` |
| `LoaderOverrides` | 结构体 | 配置加载器覆盖（主要用于测试），支持自定义管理配置路径和 MDM base64 偏好设置 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ConfigLayerEntry::new` | `(name, config) -> Self` | 创建配置层条目，自动计算版本指纹 |
| `ConfigLayerEntry::new_disabled` | `(name, config, reason) -> Self` | 创建被禁用的配置层条目 |
| `ConfigLayerEntry::config_folder` | `() -> Option<AbsolutePathBuf>` | 获取该层关联的 `.codex/` 文件夹路径 |
| `ConfigLayerEntry::as_layer` | `() -> ConfigLayer` | 转换为协议层的 `ConfigLayer` 表示 |
| `ConfigLayerStack::new` | `(layers, requirements, requirements_toml) -> io::Result<Self>` | 创建配置层栈，验证层优先级顺序 |
| `ConfigLayerStack::effective_config` | `() -> TomlValue` | 合并所有活动层，返回最终有效配置 |
| `ConfigLayerStack::origins` | `() -> HashMap<String, ConfigLayerMetadata>` | 计算每个配置字段的来源层 |
| `ConfigLayerStack::with_user_config` | `(config_toml, user_config) -> Self` | 返回替换/插入用户层后的新配置层栈 |
| `ConfigLayerStack::get_layers` | `(ordering, include_disabled) -> Vec<&ConfigLayerEntry>` | 按指定顺序返回配置层引用列表 |
| `verify_layer_ordering` | `(layers) -> io::Result<Option<usize>>`（内部） | 验证层排序正确性、用户层唯一性和项目层从根到 cwd 的顺序 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`、`codex_utils_absolute_path`、`serde_json`、`toml`
- **被引用方**: `codex-config` 的 `lib.rs` 重导出；`codex-core` 配置加载和会话初始化使用

## 实现备注

- `verify_layer_ordering` 确保：(1) 层按 `ConfigLayerSource` 的 `precedence()` 排序；(2) 最多一个 User 层；(3) Project 层从根目录到工作目录递进。
- `with_user_config` 是不可变方法，返回新的 `ConfigLayerStack` 实例，适合在配置热更新场景中使用。
- `effective_config` 仅合并活动（非禁用）层。
