# `safety_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖补丁操作的安全评估（safety assessment）功能，包括可写根目录约束检查（writable_roots_constraint）、外部沙箱的自动审批行为、粒度审批配置（GranularApprovalConfig）对越界写入的处理、显式不可读/只读路径的权限阻断，以及 .codex 项目配置文件缺失时的审批要求。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_writable_roots_constraint` | 验证工作区写入策略：目录内写入被允许、目录外写入被拒绝、显式添加父目录后目录外写入被允许 |
| `external_sandbox_auto_approves_in_on_request` | 验证外部沙箱模式在 OnRequest 策略下自动审批目录内写入 |
| `granular_with_all_flags_true_matches_on_request_for_out_of_root_patch` | 验证粒度审批（所有标志为 true）对越界写入的行为与 OnRequest 一致，均提示用户 |
| `granular_sandbox_approval_false_rejects_out_of_root_patch` | 验证粒度审批中 sandbox_approval 为 false 时直接拒绝越界写入 |
| `explicit_unreadable_paths_prevent_auto_approval_for_external_sandbox` | 验证显式标记为不可访问（None）的路径阻止外部沙箱自动审批 |
| `explicit_read_only_subpaths_prevent_auto_approval_for_external_sandbox` | 验证显式标记为只读的子路径阻止外部沙箱自动审批写入操作 |
| `missing_project_dot_codex_config_requires_approval` | 验证向缺失的 .codex/config.toml 写入时需要用户审批 |
