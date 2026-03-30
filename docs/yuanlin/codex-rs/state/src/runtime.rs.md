# `runtime.rs` — 状态运行时核心（SQLite 连接池和初始化）

## 功能概述
`StateRuntime` 是 Codex 状态持久化的核心运行时，负责初始化和管理两个 SQLite 数据库（状态库和日志库）的连接池。支持自动迁移、旧版数据库文件清理、WAL 模式和连接超时配置。提供数据库路径计算工具函数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `StateRuntime` | 结构体 | 持有 codex_home、默认 provider 和两个 SQLite 连接池 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `StateRuntime::init` | `async (codex_home, default_provider) -> Result<Arc<Self>>` | 初始化运行时，创建目录、清理旧文件、打开并迁移数据库 |
| `codex_home` | `(&self) -> &Path` | 返回配置的 Codex home 路径 |
| `state_db_path` | `(codex_home) -> PathBuf` | 计算状态数据库文件路径 |
| `logs_db_path` | `(codex_home) -> PathBuf` | 计算日志数据库文件路径 |
| `should_remove_db_file` | `(file_name, current_name, base_name) -> bool` | 判断旧版数据库文件是否应被删除 |

## 依赖关系
- **引用的 crate**: `sqlx`, `chrono`, `codex_protocol`, `serde_json`
- **被引用方**: `codex-rollout`, `codex-core`

## 实现备注
- 日志存储在专用数据库以减少锁竞争
- 日志分区大小限制为 10 MiB / 1000 行（按 thread_id 或 process_uuid 分区）
- 数据库文件名包含版本号以支持跨版本升级
