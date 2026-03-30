# `cli.rs` — Cloud Tasks CLI 参数定义

## 功能概述
使用 clap 定义 Codex Cloud Tasks 的 CLI 命令结构，包含五个子命令：`exec`（提交新任务）、`status`（查看任务状态）、`list`（列出任务）、`apply`（本地应用 diff）和 `diff`（显示统一 diff）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Cli` | 结构体 | 顶层 CLI 配置 |
| `Command` | 枚举 | 子命令：Exec, Status, List, Apply, Diff |
| `ExecCommand` | 结构体 | exec 子命令参数：query, env, attempts, branch |
| `ListCommand` | 结构体 | list 子命令参数：env, limit, cursor, json 输出 |

## 依赖关系
- **引用的 crate**: `clap`, `codex_utils_cli`
- **被引用方**: cloud-tasks 二进制入口
