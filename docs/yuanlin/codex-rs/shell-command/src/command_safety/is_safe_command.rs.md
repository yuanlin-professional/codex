# `is_safe_command.rs` — 安全命令白名单判断

## 功能概述
判断给定命令是否属于已知安全（只读）操作，可自动批准而无需用户确认。维护一个白名单包括 cat/ls/grep/git status 等，对 git 子命令进行详细检查（subcommand + flags），对 find/rg/base64/sed 检查危险选项。支持 `bash -lc "..."` 形式的复合命令分析。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_known_safe_command` | `fn(command) -> bool` | 判断命令是否已知安全 |

## 依赖关系
- **引用的 crate**: `crate::bash`, `crate::command_safety::is_dangerous_command`
- **被引用方**: codex-core 审批系统

## 实现备注
Git 全局选项（-c, --git-dir 等）会绕过安全检查使命令不安全。含 50+ 测试用例。
