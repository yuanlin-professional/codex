# `runner_pipe.rs` -- 提升路径命名管道辅助模块

## 功能概述

该文件为提升权限（elevated）Windows 沙箱运行器提供命名管道辅助功能。包括生成配对管道名、创建带有限制性 ACL（仅允许沙箱用户连接）的服务端管道、等待运行器连接、以及解析运行器可执行文件路径。该模块仅用于提升路径，传统受限令牌路径不使用这些辅助函数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PIPE_ACCESS_INBOUND` | const | 管道入站访问常量（0x1），windows-sys 0.52 未导出 |
| `PIPE_ACCESS_OUTBOUND` | const | 管道出站访问常量（0x2），windows-sys 0.52 未导出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `find_runner_exe` | `fn(codex_home: &Path, log_dir: Option<&Path>) -> PathBuf` | 解析提升命令运行器路径，优先使用 `.sandbox-bin` 下的副本 |
| `pipe_pair` | `fn() -> (String, String)` | 生成一对唯一的命名管道路径（`-in` 和 `-out` 后缀） |
| `create_named_pipe` | `fn(name, access, sandbox_username) -> io::Result<HANDLE>` | 创建以 SDDL DACL 限制访问权限的命名管道 |
| `connect_pipe` | `fn(h: HANDLE) -> io::Result<()>` | 父端等待运行器连接管道，容忍已连接状态 |

## 依赖关系

- **引用的 crate**: `rand`, `windows_sys`, `crate::helper_materialization`, `crate::winutil`
- **被引用方**: `elevated_impl.rs` 及上层的 unified_exec 会话管理

## 实现备注

- 管道名使用 `SmallRng::from_entropy()` 生成的 128 位随机数确保唯一性
- DACL 通过 SDDL 字符串 `"D:(A;;GA;;;{sandbox_sid})"` 构造，仅授予沙箱用户完全访问
- `connect_pipe` 容忍 `ERROR_PIPE_CONNECTED`（535）错误码，表示客户端已连接
