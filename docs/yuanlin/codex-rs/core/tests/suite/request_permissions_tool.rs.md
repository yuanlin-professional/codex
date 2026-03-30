# `request_permissions_tool.rs` -- 测试模块

## 测试覆盖范围
测试 request_permissions 工具的端到端行为，验证通过权限请求批准后，后续的 exec_command 和 apply_patch 操作能在沙箱策略限制外的路径执行。仅在 macOS 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `approved_folder_write_request_permissions_unblocks_later_exec_without_sandbox_args` | 批准文件夹写入权限后，exec_command 可以在被批准路径写入文件并读取内容 |
| `approved_folder_write_request_permissions_unblocks_later_apply_patch_without_prompt` | 批准文件夹写入权限后，apply_patch 可以在被批准路径创建文件，不触发额外的审批请求 |
