# `apply_patch.rs` — 测试模块

## 测试覆盖范围

验证 codex-exec 的 apply_patch 功能，包括独立 CLI 模式和通过 mock server 的工具调用模式。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_standalone_exec_cli_can_use_apply_patch` | 以 apply_patch 模式直接调用 codex-exec CLI |
| `test_apply_patch_tool` | 通过 mock server 模拟 apply_patch 自定义工具调用和函数调用 |
| `test_apply_patch_freeform_tool` | 通过 mock server 模拟自由格式的 apply_patch 工具调用 |
