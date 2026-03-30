# `read_file_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了文件读取功能的两个子模块：`slice`（基于行号范围的切片读取）和 `indentation`（基于缩进结构的代码块读取）。验证了行号标注、偏移量校验、非 UTF-8 处理、CRLF 行尾、行数限制、长行截断，以及多种语言（Rust/Python/JavaScript/C++）缩进模式下的代码块提取。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `reads_requested_range` | 验证按指定偏移量和行数读取文件内容，并带有 `Ln:` 行号前缀 |
| `errors_when_offset_exceeds_length` | 验证偏移量超出文件长度时返回 `RespondToModel` 错误 |
| `reads_non_utf8_lines` | 验证非 UTF-8 字节被替换为 Unicode 替换字符 (U+FFFD) |
| `trims_crlf_endings` | 验证 CRLF 行尾被正确去除 |
| `respects_limit_even_with_more_lines` | 验证读取行数受限制参数约束 |
| `truncates_lines_longer_than_max_length` | 验证超长行被截断到 `MAX_LINE_LENGTH` |
| `indentation_mode_captures_block` | 验证缩进模式能正确捕获锚定行所在的代码块 |
| `indentation_mode_expands_parents` | 验证 `max_levels` 参数可向上展开父级代码块 |
| `indentation_mode_respects_sibling_flag` | 验证 `include_siblings` 参数控制是否包含同级代码块 |
| `indentation_mode_handles_python_sample` | 验证 Python 代码的缩进结构解析（类方法级别） |
| `indentation_mode_handles_javascript_sample` | 验证 JavaScript 代码的嵌套对象/函数缩进解析（当前 `#[ignore]`） |
| `indentation_mode_handles_cpp_sample_shallow` | 验证 C++ 代码浅层（max_levels=1）缩进提取 |
| `indentation_mode_handles_cpp_sample` | 验证 C++ 代码深层（max_levels=2）缩进提取 |
| `indentation_mode_handles_cpp_sample_no_headers` | 验证 `include_header=false` 时跳过注释头 |
| `indentation_mode_handles_cpp_sample_siblings` | 验证 C++ 代码中 `include_siblings=true` 的兄弟块提取 |
