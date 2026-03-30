# `is_dangerous_command.rs` — 危险命令黑名单判断

## 功能概述
判断给定命令是否可能具有破坏性。当前主要检测 `rm -rf`/`rm -f`（含 sudo 前缀），以及 Windows 平台的特定危险操作。支持 `bash -lc "..."` 形式的复合命令分析。还提供 git 全局选项检测和子命令查找的公共辅助函数。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `command_might_be_dangerous` | `fn(command) -> bool` | 判断命令是否可能危险 |
| `find_git_subcommand` | `fn(command, subcommands) -> Option<(usize, &str)>` | 查找 git 子命令位置 |
| `git_global_option_requires_prompt` | `fn(arg) -> bool` | 判断 git 全局选项是否需要确认 |
| `executable_name_lookup_key` | `fn(raw) -> Option<String>` | 从路径提取可执行文件名 |

## 依赖关系
- **引用的 crate**: `crate::bash`（Windows 还引用 `windows_dangerous_commands`）
- **被引用方**: `is_safe_command`, codex-core 审批系统
