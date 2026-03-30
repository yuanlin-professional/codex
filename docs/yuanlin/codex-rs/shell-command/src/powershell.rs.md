# `powershell.rs` — PowerShell 命令解析与可执行文件查找

## 功能概述
提供 PowerShell 相关的命令解析工具：从 PowerShell 调用中提取脚本体、为脚本添加 UTF-8 输出编码前缀。还提供在系统中查找 `pwsh.exe` 和 `powershell.exe` 可执行文件的阻塞查找函数。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `extract_powershell_command` | `fn(command) -> Option<(&str, &str)>` | 从调用参数中提取 (shell, script) |
| `prefix_powershell_script_with_utf8` | `fn(command) -> Vec<String>` | 为脚本添加 UTF-8 前缀 |
| `try_find_pwsh_executable_blocking` | `fn() -> Option<AbsolutePathBuf>` | 查找 pwsh.exe |
| `try_find_powershell_executable_blocking` | `fn() -> Option<AbsolutePathBuf>` | 查找 powershell.exe |

## 依赖关系
- **引用的 crate**: `codex_utils_absolute_path`, `which`
- **被引用方**: `command_safety`, `parse_command`
