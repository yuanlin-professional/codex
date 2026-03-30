# `shell_snapshot.rs` — Shell 环境快照捕获与管理

## 功能概述

该文件实现了 Shell 环境快照功能，用于在 Codex 会话启动时捕获用户 Shell 的完整环境状态（函数定义、shell 选项、别名、导出变量）并保存为可 source 的脚本文件。快照机制支持 Zsh、Bash、Sh 和 PowerShell 四种 Shell 类型，通过在登录 Shell 中执行专用快照脚本来捕获环境，随后进行验证和原子化重命名。同时提供过期快照的自动清理功能，基于关联会话的 rollout 文件修改时间判断是否过期（3 天保留期）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ShellSnapshot` | struct | Shell 快照实例，包含快照文件路径和工作目录；Drop 时自动删除快照文件 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ShellSnapshot::start_snapshotting` | `fn(codex_home, session_id, session_cwd, shell, telemetry) -> watch::Sender<...>` | 启动初始快照任务，返回快照更新通道 |
| `ShellSnapshot::refresh_snapshot` | `fn(...)` | 刷新已有快照（复用通道） |
| `ShellSnapshot::try_new` | `async fn(...) -> Result<Self, &'static str>` | 核心快照创建逻辑：生成临时文件、验证、原子重命名 |
| `write_shell_snapshot` | `async fn(shell_type, output_path, cwd) -> Result<PathBuf>` | 执行快照脚本并写入文件 |
| `capture_snapshot` | `async fn(shell, cwd) -> Result<String>` | 根据 Shell 类型选择对应快照脚本执行 |
| `validate_snapshot` | `async fn(shell, snapshot_path, cwd) -> Result<()>` | 验证生成的快照文件可以被正确 source |
| `cleanup_stale_snapshots` | `async fn(codex_home, active_session_id) -> Result<()>` | 清理过期或孤儿快照文件 |
| `zsh_snapshot_script` | `fn() -> String` | 生成 Zsh 快照脚本（函数、setopts、别名、导出变量） |
| `bash_snapshot_script` | `fn() -> String` | 生成 Bash 快照脚本 |
| `sh_snapshot_script` | `fn() -> String` | 生成 POSIX sh 快照脚本 |

## 依赖关系

- **引用的 crate**: `tokio`（异步文件操作、进程管理、超时控制）、`anyhow`（错误处理）、`codex_otel`（遥测指标）、`tracing`
- **被引用方**: `crate::shell::Shell`（持有快照 watch 接收端）、会话初始化逻辑

## 实现备注

- 快照文件以 `{session_id}.{nonce}.{extension}` 命名，nonce 使用纳秒时间戳保证唯一性。
- 快照创建流程：先写入临时文件（`.tmp-{nonce}`），验证通过后原子重命名为正式文件名。
- `ShellSnapshot` 实现了 `Drop` trait，析构时自动删除快照文件以防止泄漏。
- 每个快照脚本都以 `# Snapshot file` 标记开头，`strip_snapshot_preamble` 会截取标记后的内容以去除 Shell 初始化的额外输出。
- `EXCLUDED_EXPORT_VARS`（PWD、OLDPWD）会从导出变量中排除，避免覆盖实际工作目录。
- 快照命令有 10 秒超时限制，防止用户 Shell 配置中的交互式提示导致永久挂起。
- 在 Unix 系统上通过 `detach_from_tty` 分离 TTY，防止快照进程接收终端信号。
