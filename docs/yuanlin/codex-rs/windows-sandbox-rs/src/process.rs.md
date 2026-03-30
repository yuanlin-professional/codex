# `process.rs` -- Windows 进程创建与管道管理

## 功能概述

该文件提供以受限令牌启动 Windows 进程的核心功能。主要包括使用 `CreateProcessAsUserW` 以指定令牌启动进程、构建 Unicode 环境变量块、通过匿名管道配置子进程的 stdin/stdout/stderr、以及从句柄循环读取数据的辅助函数。支持管道模式（独立 stdout/stderr）和合并 stderr 到 stdout 两种模式，以及 stdin 开启/关闭控制。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CreatedProcess` | struct | 已创建的进程信息，包含 PROCESS_INFORMATION、STARTUPINFOW 和桌面引用 |
| `StdinMode` | enum | 子进程 stdin 模式：`Closed` 或 `Open` |
| `StderrMode` | enum | stderr 模式：`MergeStdout`（合并到 stdout）或 `Separate`（独立管道） |
| `PipeSpawnHandles` | struct | 管道启动句柄集合，包含 PI、stdin_write、stdout_read、stderr_read |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_process_as_user` | `unsafe fn(h_token, argv, cwd, env_map, logs_base_dir, stdio, use_private_desktop) -> Result<CreatedProcess>` | 核心函数：以指定令牌和参数创建进程 |
| `spawn_process_with_pipes` | `fn(h_token, argv, cwd, env_map, stdin_mode, stderr_mode, use_private_desktop) -> Result<PipeSpawnHandles>` | 使用匿名管道启动进程并返回管道句柄 |
| `make_env_block` | `fn(env: &HashMap<String, String>) -> Vec<u16>` | 构建排序的 Unicode 环境变量块 |
| `read_handle_loop` | `fn<F>(handle: HANDLE, on_chunk: F) -> JoinHandle<()>` | 启动线程循环读取句柄数据直到 EOF |

## 依赖关系

- **引用的 crate**: `windows_sys`（Threading、Pipes、Console API）, `anyhow`, `crate::desktop`, `crate::winutil`, `crate::logging`
- **被引用方**: `command_runner_win.rs`, 以及其他需要创建沙箱进程的模块

## 实现备注

- 环境变量块按键名大写排序以满足 Windows 对环境块的排序要求
- 使用 `STARTF_USESTDHANDLES` 和 `SetHandleInformation(HANDLE_FLAG_INHERIT)` 确保句柄可被子进程继承
- `spawn_process_with_pipes` 在失败时正确清理所有已创建的管道句柄
- `read_handle_loop` 使用 8192 字节缓冲区循环读取
