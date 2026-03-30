# `powershell_parser.rs` — PowerShell AST 解析器进程管理

## 功能概述
管理一个长期运行的 PowerShell 子进程，通过 JSON stdin/stdout 协议调用 PowerShell 内置 AST 解析器。使用进程缓存避免重复启动开销，在解析失败时自动重试一次。将 PowerShell 脚本编码为 UTF-16 LE Base64 传递。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PowershellParseOutcome` | 枚举 | 解析结果：Commands(命令列表)/Unsupported/Failed |
| `PowershellParserProcess` | 结构体 | PowerShell 解析器子进程管理 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_with_powershell_ast` | `fn(executable, script) -> PowershellParseOutcome` | 使用 PowerShell AST 解析脚本 |

## 依赖关系
- **引用的 crate**: `base64`, `serde`
- **被引用方**: `windows_safe_commands.rs`
