# `project_root_markers.rs` — 项目根目录标记检测

## 功能概述

提供从配置中读取项目根目录标记（project root markers）的功能，以及默认标记列表。项目根目录标记用于自动检测项目根目录（如通过 `.git` 目录判断）。支持用户在 `config.toml` 中自定义标记列表，空数组表示禁用根目录检测。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `project_root_markers_from_config` | `(config: &TomlValue) -> io::Result<Option<Vec<String>>>` | 从合并后的 TOML 配置中读取 `project_root_markers` 数组，未指定返回 `None` |
| `default_project_root_markers` | `() -> Vec<String>` | 返回默认标记列表 `[".git"]` |

## 依赖关系

- **引用的 crate**: `toml`、`std::io`
- **被引用方**: `codex-config` 的 `lib.rs` 重导出，供 `codex-core` 使用

## 实现备注

- 当 `project_root_markers` 为空数组时返回 `Ok(Some(Vec::new()))`，调用方据此禁用根目录自动检测。
- 非字符串数组元素会返回 `InvalidData` 错误。
