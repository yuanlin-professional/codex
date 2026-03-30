# `apply_patch_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `apply_patch.rs` 中的文件路径提取和写权限计算逻辑，验证补丁操作的权限判断在不同沙盒策略下的正确性。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `approval_keys_include_move_destination` | 验证当补丁包含文件移动（`Move to`）操作时，`file_paths_for_action` 同时返回源路径和目标路径 |
| `write_permissions_for_paths_skip_dirs_already_writable_under_workspace_root` | 验证当文件位于工作区根目录下（已有写权限）时，`write_permissions_for_paths` 返回 `None`，不请求额外权限 |
| `write_permissions_for_paths_keep_dirs_outside_workspace_root` | 验证当文件位于工作区根目录之外时，`write_permissions_for_paths` 正确返回需要额外写权限的路径 |
