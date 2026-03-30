# `list_dir_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `list_dir.rs` 中的目录列表核心功能，包括递归遍历、分页、排序、深度控制、符号链接识别和边界条件处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `lists_directory_entries` | 验证多层目录结构的递归遍历（depth=3），包括文件、子目录和符号链接（Unix）的正确展示与缩进 |
| `errors_when_offset_exceeds_entries` | 验证当 offset 超出实际条目数量时返回 `RespondToModel` 错误 |
| `respects_depth_parameter` | 分别测试 depth=1/2/3，验证递归深度限制的正确性：depth=1 只显示第一层，depth=2 展开一层子目录，以此类推 |
| `paginates_in_sorted_order` | 验证分页功能在排序后正确工作：第一页（offset=1, limit=2）和第二页（offset=3, limit=2）返回正确的有序子集 |
| `handles_large_limit_without_overflow` | 验证当 limit 为 `usize::MAX` 时不会发生整数溢出，正确返回从 offset 开始的所有条目 |
| `indicates_truncated_results` | 创建 40 个文件后以 limit=25 查询，验证返回 25 条结果外加一条 "More than 25 entries found" 截断提示 |
| `truncation_respects_sorted_order` | 验证截断（limit=3）时按排序顺序返回前 3 条结果，并正确显示截断提示 |
