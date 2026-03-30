# `apply_patch_tests.rs` — 测试模块

## 测试覆盖范围
验证 Apply Patch 运行时的审批逻辑、Guardian 审批请求构建以及沙箱命令构建。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `wants_no_sandbox_approval_granular_respects_sandbox_flag` | Granular 审批配置中 `sandbox_approval` 标志的尊重情况 |
| `guardian_review_request_includes_patch_context` | Guardian 审批请求包含完整的补丁上下文（cwd、files、patch） |
| `build_sandbox_command_prefers_configured_codex_self_exe_for_apply_patch` | (Unix) 优先使用配置的 `codex_self_exe` 路径 |
| `build_sandbox_command_falls_back_to_current_exe_for_apply_patch` | (Unix) 无配置时回退到 `std::env::current_exe()` |
