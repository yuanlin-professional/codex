# `skills_config.rs` — 技能（Skills）配置类型定义

## 功能概述

定义 Codex 技能系统的配置类型，用于从 `config.toml` 中反序列化技能相关设置。支持按路径或名称选择性启用/禁用特定技能，以及控制内置技能包（bundled skills）的整体开关。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillConfig` | 结构体 | 单个技能配置项：可通过 `path`（绝对路径）或 `name` 选择器匹配技能，`enabled` 控制启用状态 |
| `SkillsConfig` | 结构体 | 技能配置顶层容器：包含可选的 `bundled`（内置技能配置）和 `config`（技能配置列表） |
| `BundledSkillsConfig` | 结构体 | 内置技能包配置，`enabled` 字段默认为 `true` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `TryFrom<toml::Value> for SkillsConfig` | trait 实现 | 支持从 TOML 值直接反序列化为 `SkillsConfig` |

## 依赖关系

- **引用的 crate**: `serde`、`schemars`、`codex_utils_absolute_path`
- **被引用方**: `codex-config` 的 `lib.rs` 通过 `pub use` 重导出这些类型

## 实现备注

- 所有结构体均标注 `#[schemars(deny_unknown_fields)]`，确保配置文件中不会出现未知字段。
- `BundledSkillsConfig::default()` 将 `enabled` 设为 `true`，即默认启用内置技能。
