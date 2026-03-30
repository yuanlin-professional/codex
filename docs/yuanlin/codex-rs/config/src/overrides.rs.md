# `overrides.rs` — CLI 配置覆盖层构建

## 功能概述

提供将命令行传入的点分路径键值对（如 `model.name = "gpt-5"`）转换为 TOML 值树的能力。这些覆盖值作为最高优先级的配置层，合并到 Codex 的多层配置系统中。支持任意深度的嵌套路径，自动创建中间 Table 节点。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_cli_overrides_layer` | `(cli_overrides: &[(String, TomlValue)]) -> TomlValue` | 将多个点分路径覆盖值构建为一棵 TOML Table 值树 |
| `default_empty_table` | `() -> TomlValue` | 创建一个空的 TOML Table 值（内部辅助） |
| `apply_toml_override` | `(root, path, value)` | 将单个点分路径覆盖应用到 TOML 值树上 |

## 依赖关系

- **引用的 crate**: `toml`
- **被引用方**: `codex-config` 的配置加载流程，通过 `build_cli_overrides_layer` 在 `lib.rs` 中重导出

## 实现备注

- 路径按 `.` 分割为段，逐层创建或进入嵌套 Table，最后一段设置实际值。
- 若当前节点不是 Table 类型，会被强制替换为新的 Table。
