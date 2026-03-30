# `grep_files_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `grep_files.rs`（或相关模块）中的文件搜索功能，包括 ripgrep（rg）搜索结果的解析、glob 过滤、结果数量限制以及无匹配情况的处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `parses_basic_results` | 验证 `parse_results` 能正确将 rg 输出的文件路径按行解析为字符串向量 |
| `parse_truncates_after_limit` | 验证 `parse_results` 在达到指定数量限制后截断结果 |
| `run_search_returns_results` | 集成测试：在临时目录中创建文件后执行 rg 搜索，验证返回匹配文件 |
| `run_search_with_glob_filter` | 集成测试：验证 glob 过滤参数（如 `*.rs`）能正确限制搜索范围 |
| `run_search_respects_limit` | 集成测试：验证搜索结果数量不超过指定的 limit 参数 |
| `run_search_handles_no_matches` | 集成测试：验证当无匹配结果时返回空向量而非错误 |
