# `elevated_impl.rs` -- Windows 提升权限沙箱命令执行实现

## 功能概述

该文件实现了 Windows 平台上通过提升权限（elevated）方式运行沙箱命令的核心逻辑。它定义了 `ElevatedSandboxCaptureRequest` 结构体作为请求参数，并在 Windows 平台上通过 `CreateProcessWithLogonW` 以沙箱用户身份启动命令运行器进程，通过命名管道进行 IPC 通信，捕获子进程的 stdout/stderr 输出和退出码。非 Windows 平台提供一个桩实现（stub）直接返回错误。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ElevatedSandboxCaptureRequest<'a>` | struct | 沙箱命令执行请求参数，包含策略、命令、工作目录、环境变量、超时等 |
| `CaptureResult` | struct (stub) | 非 Windows 平台的命令执行结果，包含 exit_code、stdout、stderr、timed_out |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_windows_sandbox_capture` | `fn(ElevatedSandboxCaptureRequest) -> Result<CaptureResult>` | 主入口函数：解析策略、准备环境变量、创建管道、以沙箱用户启动 runner 进程并捕获输出 |
| `find_git_root` | `fn(start: &Path) -> Option<PathBuf>` | 从给定路径向上查找 git 仓库根目录，支持 gitfile 重定向 |
| `inject_git_safe_directory` | `fn(env_map, cwd, logs_base_dir)` | 向环境变量注入 `git safe.directory` 配置，使沙箱用户能操作非自身所有的仓库 |
| `create_named_pipe` | `fn(name, access, sandbox_sid) -> io::Result<HANDLE>` | 创建仅允许沙箱用户连接的命名管道（通过 SDDL DACL） |
| `connect_pipe` | `fn(h: HANDLE) -> io::Result<()>` | 等待客户端连接到命名管道，容忍已连接状态 |
| `pipe_name` | `fn(suffix: &str) -> String` | 生成唯一随机命名管道路径 |

## 依赖关系

- **引用的 crate**: `anyhow`, `rand`, `windows_sys`, `dunce`, 以及 crate 内部模块 `acl`, `allow`, `cap`, `env`, `helper_materialization`, `identity`, `ipc_framed`, `logging`, `policy`, `token`, `winutil`
- **被引用方**: 作为 Windows 沙箱执行的核心实现，被上层沙箱调度模块调用

## 实现备注

- Windows 实现使用 `CreateProcessWithLogonW` 以沙箱用户身份启动 runner 进程，并通过 `LOGON_WITH_PROFILE` 标志加载用户配置文件
- 命名管道使用 SDDL 安全描述符限制仅沙箱用户可连接
- 通过 `SetErrorMode` 抑制 WER/UI 弹窗以便收集退出码
- 包含测试模块验证网络访问策略在不同 `SandboxPolicy` 下的行为
- 非 Windows 平台使用 `stub` 模块提供空实现，直接 bail
