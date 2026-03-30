# `parse_command.rs` — 命令解析元数据提取

## 功能概述
解析任意 shell 命令并提取人类可读的结构化元数据（`ParsedCommand`）。处理 bash -lc、PowerShell、管道、逻辑运算符等复杂命令形式，识别常见工具（git、cargo、npm、python 等）的子命令、文件参数和标志位。将连续重复的命令折叠为单条摘要。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_command` | `fn(command) -> Vec<ParsedCommand>` | 主入口：解析命令并返回去重的结构化摘要 |
| `shlex_join` | `fn(tokens) -> String` | 将 token 列表连接为 shell 安全字符串 |
| `extract_shell_command` | `fn(command) -> Option<(&str, &str)>` | 提取 shell 和脚本体 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `shlex`
- **被引用方**: TUI 命令摘要显示

## 实现备注
测试紧跟实现代码（非文件底部），覆盖 150+ 场景。代码复杂度高，推荐通过添加测试让 Codex 修复实现。
