# `windows_safe_commands.rs` — Windows 安全命令白名单

## 功能概述
在 Windows 上保守地只允许明确只读的 PowerShell 调用通过自动批准。使用真实的 PowerShell AST 解析器（通过子进程驱动的 `powershell_parser.ps1`）解析脚本，然后逐命令验证是否在安全列表中。对 Git 命令额外检查全局选项和子命令。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_safe_command_windows` | `fn(command) -> bool` | Windows 安全命令判断入口 |

## 依赖关系
- **引用的 crate**: `crate::command_safety::powershell_parser`
- **被引用方**: `is_safe_command.rs`

## 实现备注
安全白名单包括 Get-ChildItem/Get-Content/Select-String/Measure-Object/Test-Path 等。禁止重定向、调用运算符、子表达式和所有写入型 cmdlet。仅在 Windows 上编译测试。
