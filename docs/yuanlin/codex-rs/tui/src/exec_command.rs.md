# `exec_command.rs` — 命令字符串处理工具

## 功能概述

本文件提供 Shell 命令字符串的转义、解析和路径处理工具函数。主要功能包括：将命令参数数组转义为可安全执行的命令行字符串、从 `bash -lc` / `zsh -lc` 包装中提取实际脚本内容、将命令字符串拆分为参数数组（支持往返一致性检查）、以及将绝对路径相对于 HOME 目录进行简化。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `escape_command` | `fn(command: &[String]) -> String` | 使用 shlex 转义命令参数数组为字符串 |
| `strip_bash_lc_and_escape` | `fn(command: &[String]) -> String` | 从 shell 包装中提取实际脚本内容 |
| `split_command_string` | `fn(command: &str) -> Vec<String>` | 将命令字符串拆分为参数数组，保证往返一致性 |
| `relativize_to_home` | `fn(path) -> Option<PathBuf>` | 将绝对路径转为相对于 HOME 目录的路径 |

## 依赖关系

- **引用的 crate**: `codex_shell_command`（Shell 命令解析）、`dirs`（HOME 目录）、`shlex`
- **被引用方**: `history_cell.rs`、`diff_render.rs`、`app.rs`
