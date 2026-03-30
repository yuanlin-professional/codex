# `command_runner_win.rs` -- Windows 提升沙箱命令运行器主程序

## 功能概述

该文件是提升权限（elevated）沙箱路径中命令运行器进程的入口实现。CLI 以沙箱用户身份启动此二进制文件，它通过命名管道连接父进程，读取 `SpawnRequest` 帧，基于沙箱策略创建受限令牌，然后通过 ConPTY（tty 模式）或管道（非 tty 模式）启动子进程。运行器将子进程的 stdout/stderr 输出以帧格式流回父进程，接收 stdin/terminate 帧，并在子进程退出后发送最终的 exit 帧。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `IpcSpawnedProcess` | struct | 已启动的 IPC 进程句柄集合，包含 PI、stdout/stderr/stdin 句柄、ConPTY 句柄 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `fn() -> Result<()>` | 入口函数：解析管道参数、建立 IPC、启动子进程、流转 I/O、等待退出 |
| `spawn_ipc_process` | `fn(req: &SpawnRequest) -> Result<IpcSpawnedProcess>` | 根据策略创建受限令牌并启动子进程（ConPTY 或管道模式） |
| `spawn_output_reader` | `fn(writer, handle, stream, log_dir) -> JoinHandle<()>` | 启动线程将子进程输出转换为 Output 帧发送给父进程 |
| `spawn_input_loop` | `fn(reader, stdin_handle, process_handle, log_dir) -> JoinHandle<()>` | 启动线程接收 Stdin/Terminate 帧并转发给子进程 |
| `effective_cwd` | `fn(req_cwd: &Path, log_dir: Option<&Path>) -> PathBuf` | 根据 ACL mutex 状态决定使用 junction CWD 还是原始 CWD |
| `create_job_kill_on_close` | `unsafe fn() -> Result<HANDLE>` | 创建 Job Object 并设置 `KILL_ON_JOB_CLOSE` 标志 |
| `open_pipe` | `fn(name: &str, access: u32) -> Result<HANDLE>` | 打开父进程创建的命名管道 |

## 依赖关系

- **引用的 crate**: `windows_sys`（进程、管道、Job Object、ConPTY API）, `anyhow`, `codex_windows_sandbox`
- **被引用方**: 作为独立二进制文件被 `elevated_impl.rs` 中的 `CreateProcessWithLogonW` 启动

## 实现备注

- 仅在 `target_os = "windows"` 下编译
- 使用 Job Object 的 `KILL_ON_JOB_CLOSE` 确保运行器退出时子进程也被终止
- 超时通过 `WaitForSingleObject` 的 timeout 参数实现，超时后调用 `TerminateProcess` 并返回退出码 192（128+64）
- 进程退出时使用 `std::process::exit(exit_code)` 确保退出码传播
- 通过 `#[path]` 属性包含 `cwd_junction.rs` 和 `read_acl_mutex.rs` 模块
