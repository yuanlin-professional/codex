# `value.rs` -- V8 值序列化与转换工具

## 功能概述

本模块提供了 V8 JavaScript 值与 Rust 类型之间的转换工具函数。包括：将 V8 值序列化为文本输出（支持原始类型和 JSON 序列化）、将 V8 值标准化为图片内容项（验证 URL 格式和 detail 级别）、V8 值与 JSON 的双向转换、以及 V8 异常到错误文本的提取。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `serialize_output_text` | `pub fn serialize_output_text(scope, value) -> Result<String, String>` | 将 V8 值序列化为文本：原始类型直接转换，对象类型 JSON 序列化 |
| `normalize_output_image` | `pub fn normalize_output_image(scope, value) -> Result<FunctionCallOutputContentItem, ()>` | 将 V8 值标准化为图片内容项，验证 URL 和 detail 参数 |
| `v8_value_to_json` | `pub fn v8_value_to_json(scope, value) -> Result<Option<JsonValue>, String>` | 将 V8 值转换为 serde_json::Value |
| `json_to_v8` | `pub fn json_to_v8(scope, value) -> Option<v8::Local<v8::Value>>` | 将 JSON 值转换为 V8 值 |
| `value_to_error_text` | `pub fn value_to_error_text(scope, value) -> String` | 从 V8 异常值提取错误文本（优先使用 `stack` 属性） |
| `throw_type_error` | `pub fn throw_type_error(scope, message)` | 在 V8 scope 中抛出类型错误 |

## 依赖关系

- **引用的 crate**: `v8`, `serde_json`, `crate::response`
- **被引用方**: `runtime/callbacks.rs`（处理全局函数回调时使用）

## 实现备注

- `serialize_output_text` 对 undefined/null/boolean/number/string/bigint 等原始类型直接转换，避免不必要的 JSON 序列化开销
- `normalize_output_image` 验证 URL 必须以 `http://`、`https://` 或 `data:` 开头
- 使用 `v8::TryCatch` 捕获 JSON 序列化过程中的异常
