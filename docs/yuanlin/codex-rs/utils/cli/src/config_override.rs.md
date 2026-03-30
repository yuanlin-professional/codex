# `config_override.rs` — CLI 配置覆盖参数处理

## 功能概述

本文件提供 `CliConfigOverrides` 结构体，支持通过 `-c key=value` 或 `--config key=value` 在命令行覆盖配置项。原始字符串收集后通过 `parse_overrides` 解析为 `(path, toml::Value)` 键值对列表，右侧值优先尝试 TOML 解析、失败则作为原始字符串。`apply_on_value` 方法将解析后的覆盖项应用到 TOML 配置树上，自动创建中间层级的表。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CliConfigOverrides` | struct | 收集 `-c key=value` 参数的 clap 结构体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_overrides` | `fn(&self) -> Result<Vec<(String, Value)>, String>` | 解析原始覆盖字符串为键值对 |
| `apply_on_value` | `fn(&self, target: &mut Value) -> Result<(), String>` | 将覆盖项应用到 TOML 值树 |
| `canonicalize_override_key` | `fn(key) -> String` | 规范化特殊键名别名 |

## 依赖关系

- **引用的 crate**: `clap`, `serde`, `toml`
- **被引用方**: CLI 入口模块

## 实现备注

- `use_legacy_landlock` 键自动规范化为 `features.use_legacy_landlock`。
- 值解析失败时会去除引号后作为字符串字面量。
- 测试覆盖了基本标量、布尔值、数组、内联表和键别名规范化。
