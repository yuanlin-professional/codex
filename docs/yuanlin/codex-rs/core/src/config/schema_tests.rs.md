# `schema_tests.rs` -- 测试模块

## 测试覆盖范围
验证 `config.toml` 的 JSON Schema 生成与项目中的 fixture 文件保持一致。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `config_schema_matches_fixture` | 验证当前代码生成的 schema 与仓库中的 `config.schema.json` fixture 文件逐字节匹配。如不一致则提示运行 `just write-config-schema` 更新，并通过 `similar::TextDiff` 显示差异。同时验证写入临时文件后与 fixture 精确匹配（包括换行符规范化）。 |
