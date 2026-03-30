# `fingerprint.rs` — 配置层指纹与来源追踪

## 功能概述

提供配置层版本指纹计算和字段来源记录功能。`version_for_toml` 通过对 TOML 值进行规范化 JSON 序列化后计算 SHA-256 哈希来生成确定性版本字符串，用于检测配置层是否发生变化。`record_origins` 递归遍历 TOML 值树，记录每个叶子节点的点分路径到对应配置层元数据的映射关系。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `record_origins` | `(value, meta, path, origins)` | 递归遍历 TOML 值树，将每个叶子路径关联到来源配置层元数据 |
| `version_for_toml` | `(value: &TomlValue) -> String` | 对 TOML 值计算 SHA-256 指纹，返回 "sha256:{hex}" 格式 |
| `canonical_json` | `(value: &JsonValue) -> JsonValue`（内部） | 将 JSON 对象的键排序以确保序列化的确定性 |

## 依赖关系

- **引用的 crate**: `sha2`、`serde_json`、`toml`、`codex_app_server_protocol`
- **被引用方**: `state.rs` 中的 `ConfigLayerEntry::new` 使用 `version_for_toml`；`ConfigLayerStack::origins` 使用 `record_origins`

## 实现备注

- 规范化 JSON 通过递归排序所有对象键来保证相同内容的 TOML 值始终产生相同的哈希，即使原始键顺序不同。
- 数组中的元素按索引作为路径段记录来源（如 `foo.0`、`foo.1`）。
