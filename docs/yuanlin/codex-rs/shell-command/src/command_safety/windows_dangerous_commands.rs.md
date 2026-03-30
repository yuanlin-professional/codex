# `windows_dangerous_commands.rs` — Windows 危险命令检测

## 功能概述
检测 Windows 平台上的危险命令模式：PowerShell 中使用 `Start-Process`/`Invoke-Item` 打开 URL、CMD 中 `start` 打开 URL、直接调用浏览器可执行文件打开 URL、`Remove-Item -Force` 强制删除、CMD 的 `del /f` 和 `rd /s /q` 等破坏性操作。支持 PowerShell 和 CMD 的多种语法变体。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_dangerous_command_windows` | `fn(command) -> bool` | Windows 危险命令检测入口 |

## 依赖关系
- **引用的 crate**: `regex`, `url`, `shlex`
- **被引用方**: `is_dangerous_command.rs`

## 实现备注
含 30+ 测试用例覆盖 PowerShell/CMD/直接调用的各种危险模式和安全模式。
