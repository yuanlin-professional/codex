# `env.rs` -- Windows 沙箱环境变量管理

## 功能概述

本模块管理 Windows 沙箱进程的环境变量。功能包括：将 `/dev/null` 路径规范化为 Windows 的 `NUL`、设置非交互式分页器（GIT_PAGER/PAGER）、继承父进程的 PATH/PATHEXT、以及应用网络阻断环境变量（设置代理到不可达地址、禁用包管理器网络访问、禁用 Git 网络协议）。还包含 denybin 目录机制，通过注入返回错误的 .bat/.cmd 桩来阻止 ssh/scp 的执行。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `normalize_null_device_env` | `pub fn normalize_null_device_env(env_map)` | 将 `/dev/null` 路径替换为 `NUL` |
| `ensure_non_interactive_pager` | `pub fn ensure_non_interactive_pager(env_map)` | 设置 GIT_PAGER/PAGER 为 `more.com` |
| `inherit_path_env` | `pub fn inherit_path_env(env_map)` | 继承父进程 PATH 和 PATHEXT |
| `apply_no_network_to_env` | `pub fn apply_no_network_to_env(env_map) -> Result<()>` | 全面应用网络阻断环境变量 |
| `ensure_denybin` | `fn ensure_denybin(tools, denybin_dir) -> Result<PathBuf>` | 创建返回错误码的工具桩（.bat/.cmd） |

## 依赖关系

- **引用的 crate**: `anyhow`, `dirs_next`
- **被引用方**: `lib.rs`（windows_impl 模块中使用）

## 实现备注

- 网络阻断通过将 HTTP_PROXY/HTTPS_PROXY 等指向 `127.0.0.1:9`（不可达端口）实现
- 同时设置 `PIP_NO_INDEX`、`NPM_CONFIG_OFFLINE`、`CARGO_NET_OFFLINE` 等包管理器离线标志
- `GIT_SSH_COMMAND` 设置为 `cmd /c exit 1` 以阻止 Git SSH 操作
- PATHEXT 重排确保 .BAT/.CMD 优先于 .EXE，使 denybin 桩生效
