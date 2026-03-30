# `shell.rs` — Shell 类型检测与实例构建

## 功能概述

该文件定义了 Codex 支持的 Shell 类型（Zsh、Bash、PowerShell、Sh、Cmd）及其实例化逻辑。`Shell` 结构体封装了 Shell 类型、可执行文件路径和环境快照接收端。核心功能包括：根据 Shell 类型生成正确的命令行参数（`derive_exec_args`）、通过多种策略查找 Shell 可执行文件（用户指定路径 -> 系统默认 Shell -> `which` 查找 -> 硬编码回退路径）、检测用户默认 Shell 并提供平台感知的回退链。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ShellType` | enum | Shell 类型枚举：Zsh / Bash / PowerShell / Sh / Cmd |
| `Shell` | struct | Shell 实例：类型、可执行文件路径、环境快照接收器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Shell::name` | `fn(&self) -> &'static str` | 返回 Shell 名称字符串 |
| `Shell::derive_exec_args` | `fn(&self, command: &str, use_login_shell: bool) -> Vec<String>` | 根据 Shell 类型生成执行参数（如 `["/bin/zsh", "-lc", "cmd"]`） |
| `Shell::shell_snapshot` | `fn(&self) -> Option<Arc<ShellSnapshot>>` | 获取当前环境快照（如已就绪） |
| `get_shell` | `fn(shell_type, path: Option<&PathBuf>) -> Option<Shell>` | 按类型获取 Shell 实例 |
| `default_user_shell` | `fn() -> Shell` | 获取用户默认 Shell（带平台感知回退） |
| `get_shell_by_model_provided_path` | `fn(shell_path: &PathBuf) -> Shell` | 根据模型提供的路径创建 Shell 实例 |
| `get_user_shell_path` | `fn() -> Option<PathBuf>` | Unix: 通过 `getpwuid_r` 获取用户默认 Shell 路径 |
| `get_shell_path` | `fn(shell_type, provided_path, binary_name, fallback_paths) -> Option<PathBuf>` | 多策略 Shell 路径查找 |

## 依赖关系

- **引用的 crate**: `which`（PATH 搜索）、`libc`（Unix 系统调用）、`tokio::sync::watch`（快照通道）、`serde`（序列化支持）
- **被引用方**: `crate::shell_snapshot`（快照系统）、命令执行层、会话初始化

## 实现备注

- `Shell` 的 `PartialEq` 和 `Eq` 实现忽略了 `shell_snapshot` 字段，仅比较类型和路径。
- `shell_snapshot` 字段在序列化时被跳过（`skip_serializing`/`skip_deserializing`），反序列化时使用空接收器默认值。
- Unix 上使用线程安全的 `getpwuid_r`（而非 `getpwuid`）获取用户 Shell，避免 musl 静态构建下的并发竞态。缓冲区不足时会自动扩容重试（最大 1MB）。
- Shell 路径查找优先级：用户指定路径 > 系统默认 Shell > `which` 查找 > 硬编码回退路径。
- 默认 Shell 回退链在 macOS 上优先 Zsh，Linux 上优先 Bash，Windows 上优先 PowerShell。
- `ultimate_fallback_shell` 提供最后的兜底：Unix 上返回 `/bin/sh`，Windows 上返回 `cmd.exe`。
- PowerShell 的 `derive_exec_args` 在非登录模式下添加 `-NoProfile` 标志。
