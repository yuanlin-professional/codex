# `apply_command_e2e.rs` — 测试模块

## 测试覆盖范围
端到端验证 `apply_diff_from_task` 函数：从 JSON fixture 加载任务响应，在临时 Git 仓库中应用 diff，并校验文件创建和合并冲突行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `test_apply_command_creates_fibonacci_file` | 在干净仓库中应用 diff 后，验证 fibonacci.js 文件被正确创建并包含预期内容和行数 |
| `test_apply_command_with_merge_conflicts` | 在已有冲突文件的仓库中应用 diff，验证操作失败并产生合并冲突标记 |
