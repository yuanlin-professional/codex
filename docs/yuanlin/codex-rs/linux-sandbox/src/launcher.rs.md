# `launcher.rs` -- bubblewrap 启动器选择

## 功能概述

本模块负责选择和执行 bubblewrap 实例：优先使用系统安装的 bwrap 二进制（通过 `PATH` 查找），若不存在则回退到构建时内嵌的版本。它还检测系统 bwrap 是否支持 `--argv0` 选项（v0.9.0+ 才有），并据此调整启动策略。对于系统 bwrap，通过 `execv` 系统调用直接执行；对于内嵌版本，通过 FFI 调用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `BubblewrapLauncher` | enum | 启动器类型：`System`（系统 bwrap + argv0 支持信息）或 `Vendored`（内嵌版本） |
| `SystemBwrapLauncher` | struct | 系统 bwrap 的路径和是否支持 `--argv0` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `exec_bwrap` | `pub fn exec_bwrap(argv, preserved_files) -> !` | 根据选择的启动器执行 bwrap |
| `preferred_bwrap_supports_argv0` | `pub fn preferred_bwrap_supports_argv0() -> bool` | 查询首选启动器是否支持 `--argv0` |
| `preferred_bwrap_launcher` | `fn preferred_bwrap_launcher() -> BubblewrapLauncher` | 使用 `OnceLock` 缓存启动器选择结果 |
| `system_bwrap_supports_argv0` | `fn system_bwrap_supports_argv0(path) -> bool` | 通过检查 `--help` 输出判断是否支持 `--argv0` |
| `make_files_inheritable` | `fn make_files_inheritable(files)` | 清除文件描述符的 `CLOEXEC` 标志，使其跨 exec 存活 |

## 依赖关系

- **引用的 crate**: `codex_sandboxing` (查找系统 bwrap), `codex_utils_absolute_path`, `libc`
- **被引用方**: `linux_run_main.rs`

## 实现备注

- 系统 bwrap 通过 `execv` 执行，所以需要清除保留文件描述符的 `CLOEXEC` 标志
- 内嵌 bwrap 通过 FFI 调用，文件描述符不跨越 exec 边界
- 启动器选择结果通过 `OnceLock` 缓存，整个进程生命周期内只检测一次
