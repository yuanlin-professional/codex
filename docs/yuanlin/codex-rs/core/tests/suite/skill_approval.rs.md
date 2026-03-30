# `skill_approval.rs` -- 测试模块

## 测试覆盖范围
测试 skill 脚本在 zsh fork 沙箱模式下的权限与审批行为。验证 skill 脚本不再走旧的 skill approval 路径，而是受 turn 级别沙箱策略管控；同时测试 workspace write 沙箱策略对工作区外写入的限制。仅在 Unix 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `shell_zsh_fork_skill_scripts_ignore_declared_permissions` | skill 脚本声明的 permissions（如文件写入路径）被忽略，不触发 skill approval 请求，脚本受 turn 沙箱限制，无法写入工作区外路径 |
| `shell_zsh_fork_still_enforces_workspace_write_sandbox` | 在 WorkspaceWrite 沙箱策略下，zsh fork 执行的命令无法写入工作区外路径，验证沙箱拒绝输出 |
