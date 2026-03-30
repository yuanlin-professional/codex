# `turn_diff_tracker_tests.rs` — 测试模块

## 测试覆盖范围

轮次 diff 追踪器（TurnDiffTracker），包括文件添加/更新/删除的累积 diff 生成、文件移动与重命名、基线快照管理、二进制文件 diff、文件名含空格的 diff 处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `accumulates_add_and_update` | 累积文件添加和更新的 diff |
| `accumulates_delete` | 累积文件删除的 diff |
| `accumulates_move_and_update` | 累积文件移动和更新的 diff |
| `move_without_1change_yields_no_diff` | 仅移动无内容变更时不产生 diff |
| `move_declared_but_file_only_appears_at_dest_is_add` | 声明移动但文件仅出现在目标路径时视为添加 |
| `update_persists_across_new_baseline_for_new_file` | 更新在新文件基线创建后持续保留 |
| `binary_files_differ_update` | 二进制文件更新显示 "Binary files differ" |
| `filenames_with_spaces_add_and_update` | 文件名含空格时的添加和更新 diff |
