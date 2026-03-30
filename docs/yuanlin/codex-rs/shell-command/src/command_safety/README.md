# shell-command/src/command_safety — 命令安全检查

## 功能概述
Shell 命令的安全性检查模块，判断命令是否安全或危险。

## 目录结构
| 文件 | 说明 |
|------|------|
| `is_dangerous_command.rs` | 危险命令检查 |
| `is_safe_command.rs` | 安全命令检查 |
| `mod.rs` | 模块入口 |
| `powershell_parser.ps1` | PowerShell 解析脚本 |
| `powershell_parser.rs` | PowerShell 解析器 |
| `windows_dangerous_commands.rs` | Windows 危险命令 |
| `windows_safe_commands.rs` | Windows 安全命令 |
