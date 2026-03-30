# `shell_snapshot.rs` — 测试模块

## 测试覆盖范围

Shell 快照（Shell Snapshot）功能的集成测试，覆盖 `exec_command`（统一执行工具）和 `shell_command` 两种工具在启用 `ShellSnapshot` feature flag 后的快照生成、加载、环境变量策略保留、`apply_patch` 拦截以及关闭后清理等行为。测试针对 Linux、macOS 和 Windows 三个平台分别编写（部分以 `#[ignore]` 标注）。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `linux_unified_exec_uses_shell_snapshot` | Linux 下 `exec_command` 使用 shell 快照运行命令，验证命令参数、快照路径、快照内容包含关键段以及 stdout 输出 |
| `linux_shell_command_uses_shell_snapshot` | Linux 下 `shell_command` 使用 shell 快照运行命令，验证与 `exec_command` 对等的快照功能 |
| `shell_command_snapshot_preserves_shell_environment_policy_set` | 验证 `shell_command` 的快照加载后，`shell_environment_policy.set` 中的环境变量（如 PATH）仍能正确覆盖快照值 |
| `linux_unified_exec_snapshot_preserves_shell_environment_policy_set` | 验证 `exec_command` 的快照加载后，环境变量策略同样被正确应用 |
| `shell_command_snapshot_still_intercepts_apply_patch` | 在启用快照的情况下，`shell_command` 中内联调用 `apply_patch` 仍被正确拦截并产生 `PatchApplyBegin`/`PatchApplyEnd` 事件 |
| `shell_snapshot_deleted_after_shutdown_with_skills` | Codex 关闭（Shutdown）后快照文件被自动删除 |
| `macos_unified_exec_uses_shell_snapshot` | macOS 下 `exec_command` 通过 `. "$0" && exec "$@"` 方式加载快照运行命令 |
| `windows_unified_exec_uses_shell_snapshot` | Windows 下 `exec_command` 使用 PowerShell 快照运行命令（当前被 `#[ignore]`） |
