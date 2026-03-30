# `mod.rs` (conpty) — ConPTY 进程创建与管理

## 功能概述
该文件封装了 Windows ConPTY（伪控制台）的创建和基于 ConPTY 的进程启动。它是传统受限令牌路径和提权运行路径共用的 PTY 启动入口，在需要 TTY 交互的沙盒进程中使用。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ConptyInstance` | struct | ConPTY 实例，持有句柄和管道 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_conpty` | `(cols, rows) -> Result<ConptyInstance>` | 创建 ConPTY 实例 |
| `spawn_conpty_process_as_user` | `(h_token, argv, cwd, env_map, ...) -> Result<(PI, ConptyInstance)>` | 以指定令牌用户身份启动 ConPTY 进程 |

## 依赖关系
- **引用的 crate**: `windows_sys`, `codex_utils_pty`, `anyhow`
- **被引用方**: `command_runner_win.rs`, `lib.rs`
