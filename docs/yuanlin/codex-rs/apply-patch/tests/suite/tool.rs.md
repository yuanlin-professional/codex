# `tool.rs` -- 测试模块

## 测试覆盖范围

通过 CLI 命令行测试 apply_patch 工具的基本添加和更新操作，同时验证通过 stdin 传入 patch 的方式也能正常工作。仅在非 Windows 平台编译（`#[cfg(not(target_os = "windows"))]`）。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_apply_patch_cli_add_and_update` | 先通过命令行参数添加文件，再更新该文件，验证两步操作都成功 |
| `test_apply_patch_cli_stdin_add_and_update` | 先通过 stdin 添加文件，再通过 stdin 更新该文件，验证 stdin 模式正常工作 |
