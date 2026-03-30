# `output_schema.rs` — 测试模块

## 测试覆盖范围

验证 --output-schema 标志正确将 JSON Schema 包含在请求中。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_includes_output_schema_in_request` | --output-schema 指定的 JSON Schema 以 json_schema 格式包含在请求的 text.format 字段中 |
