# `lib.rs` — JSON 到 TOML 值转换

## 功能概述

提供 `json_to_toml` 函数，将 `serde_json::Value` 递归转换为语义等价的 `toml::Value`。`null` 转为空字符串，数字优先转为整数再转浮点数，数组和对象递归转换。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `json_to_toml` | `fn(v: JsonValue) -> TomlValue` | 将 JSON 值转换为 TOML 值 |

## 依赖关系

- **引用的 crate**: `serde_json`, `toml`
- **被引用方**: 配置处理模块中需要 JSON 到 TOML 转换的地方

## 实现备注

- 测试覆盖了数字、数组、布尔、浮点、null 和嵌套对象的转换。
