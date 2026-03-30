# `shell_detect.rs` — Shell 类型检测
定义 `ShellType` 枚举（Zsh/Bash/PowerShell/Sh/Cmd）和 `detect_shell_type` 函数，通过路径名或文件名匹配检测 shell 类型。支持完整路径和简写名称的递归解析。
