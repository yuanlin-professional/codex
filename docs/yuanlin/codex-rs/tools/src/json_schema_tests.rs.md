# `json_schema_tests.rs` -- 测试模块

## 测试覆盖范围
验证 JSON Schema 解析和清洗逻辑，包括缺少 type 时的推断、嵌套属性处理和布尔 schema 转换。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_infers_object_type` | 从 properties 关键字推断 type 为 object |
| `parse_infers_array_type` | 从 items 关键字推断 type 为 array |
| `parse_infers_string_from_enum` | 从 enum 关键字推断 type 为 string |
| `parse_boolean_schema` | 布尔值 schema (true) 被转换为 string 类型 |
| `parse_nested_sanitization` | 嵌套属性递归清洗 |
