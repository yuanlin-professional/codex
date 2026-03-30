# `logs_client.rs` — Codex 日志查看命令行客户端

## 功能概述
这是一个独立的命令行工具，用于从 Codex 日志 SQLite 数据库中尾随查看日志。支持按级别、时间范围、模块路径、文件路径、线程 ID 和关键字进行过滤，带有彩色输出和 diff 格式化。支持回填历史日志和持续轮询新日志。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Args` | 结构体 | 命令行参数：codex_home、db 路径、过滤条件、轮询间隔等 |
| `LogFilter` | 结构体 | 日志过滤器：级别、时间范围、模块/文件匹配、线程 ID、搜索词 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `async () -> anyhow::Result<()>` | 入口：解析参数、回填历史、轮询新日志 |
| `resolve_db_path` | `(args) -> Result<PathBuf>` | 确定日志数据库路径 |
| `build_filter` | `(args) -> Result<LogFilter>` | 构建日志过滤器 |
| `format_row` | `(row, compact) -> String` | 格式化日志行（支持 compact 和详细模式） |
| `heuristic_formatting` | `(message) -> String` | 对 apply_patch 类消息应用 diff 着色 |

## 依赖关系
- **引用的 crate**: `codex_state`, `clap`, `owo_colors`, `chrono`, `dirs`
- **被引用方**: 终端用户直接使用

## 实现备注
- 默认回填 200 行历史日志，轮询间隔 500ms
- apply_patch 消息自动着色（绿色加行、红色删行）
