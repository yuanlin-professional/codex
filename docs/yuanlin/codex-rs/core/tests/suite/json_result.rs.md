# `json_result.rs` — 测试模块

## 测试覆盖范围
JSON 结构化输出结果功能，验证 Codex 在指定 JSON Schema 后能正确返回符合格式的 JSON 结果。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `codex_returns_json_result_for_gpt5` | 使用 gpt-5.1 模型时，Codex 返回符合指定 JSON Schema 的结构化结果 |
| `codex_returns_json_result_for_gpt5_codex` | 使用 gpt-5.1-codex 模型时，Codex 返回符合指定 JSON Schema 的结构化结果 |
