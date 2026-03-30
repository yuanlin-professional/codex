# `json_schema.rs` -- JSON Schema 子集解析

## 功能概述
定义 Codex 工具所需的 JSON Schema 子集枚举（Boolean/String/Number/Array/Object）及其解析逻辑。`sanitize_json_schema` 对原始 JSON Schema 进行清洗：为缺少 type 的 schema 推断类型（从 properties/items/enum/minimum 等关键字）、为 object 补充空 properties、为 array 补充默认 items。支持 `AdditionalProperties` 的布尔和 schema 两种形式。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `JsonSchema` | enum | JSON Schema 子集：Boolean/String/Number/Array/Object |
| `AdditionalProperties` | enum | 额外属性：Boolean(bool) 或 Schema(Box<JsonSchema>) |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_tool_input_schema` | `fn(input_schema) -> Result<JsonSchema>` | 解析并清洗 JSON Schema |
| `sanitize_json_schema` | `fn(value)` | 递归清洗 JSON Schema，推断缺失类型并补充必需字段 |

## 依赖关系
- **引用的 crate**: `serde`, `serde_json`
- **被引用方**: 所有工具定义模块

## 实现备注
- Number 类型兼容 "integer" 别名
- 类型推断优先级：properties/required/additionalProperties -> object, items/prefixItems -> array, enum/const/format -> string, minimum/maximum -> number
- 递归处理 oneOf/anyOf/allOf/prefixItems 组合器
