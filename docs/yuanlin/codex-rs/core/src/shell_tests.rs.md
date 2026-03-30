# `shell_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 Shell 检测与命令执行功能，包括各种 Shell 类型（Zsh、Bash、Sh、PowerShell、Cmd）的检测与路径获取、Fish 不支持时回退到 Zsh、Shell 命令执行验证、derive_exec_args 参数构建（普通模式和登录 shell 模式），以及默认用户 Shell 检测。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detects_zsh` | (仅 macOS) 验证检测 Zsh 返回 /bin/zsh 路径 |
| `fish_fallback_to_zsh` | (仅 macOS) 验证 Fish shell 路径回退到 Zsh |
| `detects_bash` | 验证检测 Bash 返回正确的 bash 路径 |
| `detects_sh` | 验证检测 Sh 返回正确的 sh 路径 |
| `can_run_on_shell_test` | 验证各种 Shell 类型能正确执行命令并产生输出 |
| `derive_exec_args` | 验证 Bash、Zsh、PowerShell 在普通和登录模式下的命令参数构建 |
| `test_current_shell_detects_zsh` | 验证当系统 SHELL 为 Zsh 时 default_user_shell 正确检测 |
| `detects_powershell_as_default` | (仅 Windows) 验证默认 Shell 检测为 PowerShell |
| `finds_powershell` | (仅 Windows) 验证 get_shell 能找到 PowerShell 可执行文件 |
