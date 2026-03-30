# `lib.rs` — arg0 分发机制与 PATH 别名管理

## 功能概述

实现 Codex CLI 的 arg0 分发技巧，允许单个二进制文件根据调用名称（argv[0]）执行不同功能。支持 `codex-linux-sandbox`（Linux 沙箱）、`apply_patch`（补丁应用）、`codex-execve-wrapper`（shell 提权包装）等多个别名。通过在临时目录中创建符号链接并注入到 PATH，使子进程也能通过这些别名调用同一二进制。还负责加载 `~/.codex/.env` 文件（过滤掉 `CODEX_` 前缀变量）和 Tokio 运行时构建。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Arg0DispatchPaths` | struct | 包含 codex 自身、Linux 沙箱、execve 包装器的可执行文件路径 |
| `Arg0PathEntryGuard` | struct | 持有临时目录和锁文件的 RAII 守卫，保持 PATH 条目在进程生命周期内有效 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `arg0_dispatch_or_else` | `(main_fn) -> Result<()>` | 主入口包装：先执行 arg0 分发，否则构建 Tokio 运行时并执行 main_fn |
| `arg0_dispatch` | `() -> Option<Arg0PathEntryGuard>` | 检查 argv[0] 执行对应分发，然后加载 dotenv 并创建 PATH 别名 |
| `prepend_path_entry_for_codex_aliases` | `() -> io::Result<Arg0PathEntryGuard>` | 创建临时目录、符号链接和锁文件，注入到 PATH |
| `load_dotenv` | `()` | 从 ~/.codex/.env 加载环境变量（排除 CODEX_ 前缀） |
| `janitor_cleanup` | `(temp_root) -> io::Result<()>` | 清理未锁定的过期临时目录 |

## 依赖关系

- **引用的 crate**: `codex_apply_patch`、`codex_sandboxing`、`codex_linux_sandbox`、`codex_shell_escalation`、`codex_utils_home_dir`、`dotenvy`、`tempfile`
- **被引用方**: `exec/src/main.rs`、`cli/src/main.rs`（所有二进制入口）

## 实现备注

- PATH 修改和 dotenv 加载必须在多线程 Tokio 运行时启动之前完成（因为 `set_var` 非线程安全）。
- 使用文件锁防止 janitor 删除仍在使用的临时目录。
- Tokio 运行时使用 16MB 栈大小。
