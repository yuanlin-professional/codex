# json-to-toml

## 功能概述

`codex-utils-json-to-toml` 提供了将 `serde_json::Value` 转换为语义等价的 `toml::Value` 的工具函数。用于 Codex 配置系统中 JSON 格式配置到 TOML 格式的转换。

类型映射规则：
- `Null` -> 空字符串
- `Bool` -> `Boolean`
- `Number`（整数） -> `Integer`
- `Number`（浮点） -> `Float`
- `String` -> `String`
- `Array` -> `Array`（递归转换）
- `Object` -> `Table`（递归转换）

## 目录结构

```
json-to-toml/
├── Cargo.toml
└── src/
    └── lib.rs          # json_to_toml() 函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `serde_json` | JSON Value 类型 |
| `toml` | TOML Value 类型 |

## 核心接口/API

### `json_to_toml(v: JsonValue) -> TomlValue`

将 JSON 值递归转换为 TOML 值。

```rust
use serde_json::json;

let toml_val = json_to_toml(json!({"key": 42, "nested": {"flag": true}}));
// 结果为 TomlValue::Table，包含 "key" -> Integer(42) 和 "nested" -> Table(...)
```
