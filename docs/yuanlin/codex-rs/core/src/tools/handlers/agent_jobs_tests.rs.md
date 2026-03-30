# `agent_jobs_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `agent_jobs.rs` 中的纯函数逻辑，包括 CSV 解析、CSV 转义、指令模板渲染和 CSV 表头唯一性校验。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parse_csv_supports_quotes_and_commas` | 验证 CSV 解析器能正确处理带引号的字段和内嵌逗号 |
| `csv_escape_quotes_when_needed` | 验证 `csv_escape` 函数在遇到逗号或双引号时正确添加引号和转义 |
| `render_instruction_template_expands_placeholders_and_escapes_braces` | 验证模板渲染能正确展开 `{column}` 占位符（包括含空格的列名），并将 `{{` 转义为字面量 `{` |
| `render_instruction_template_leaves_unknown_placeholders` | 验证当占位符在行数据中不存在时，模板保留原始占位符不做替换 |
| `ensure_unique_headers_rejects_duplicates` | 验证重复的 CSV 表头会触发 `RespondToModel` 错误 |
