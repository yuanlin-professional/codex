# `bash.rs` — Bash/Zsh 脚本解析器

## 功能概述
使用 tree-sitter-bash 语法解析器对 shell 脚本进行结构化分析。识别"纯单词命令序列"（仅含 `&&`, `||`, `;`, `|` 连接的简单命令）并提取每个命令的参数列表。支持双引号/单引号字符串和拼接参数。还支持 heredoc 场景的单命令前缀提取。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `try_parse_shell` | `fn(src) -> Option<Tree>` | 使用 tree-sitter-bash 解析脚本 |
| `try_parse_word_only_commands_sequence` | `fn(tree, src) -> Option<Vec<Vec<String>>>` | 提取纯单词命令序列 |
| `parse_shell_lc_plain_commands` | `fn(command) -> Option<Vec<Vec<String>>>` | 从 bash -lc "..." 提取命令序列 |
| `extract_bash_command` | `fn(command) -> Option<(&str, &str)>` | 从 [shell, -lc, script] 提取脚本 |

## 依赖关系
- **引用的 crate**: `tree_sitter`, `tree_sitter_bash`
- **被引用方**: `is_safe_command`, `is_dangerous_command`

## 实现备注
拒绝所有不安全构造：子 shell、重定向、变量/命令替换、后台运算符等。含 30+ 测试用例。
