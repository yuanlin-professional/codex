# `bwrap.rs` -- bubblewrap 文件系统沙箱参数构建

## 功能概述

本模块负责根据文件系统沙箱策略构建 bubblewrap 的命令行参数。它实现了与 macOS Seatbelt 沙箱类似的语义：默认只读文件系统，显式叠加可写根目录，`.git`/`.codex` 等敏感子路径即使在可写根目录内也保持只读。支持全磁盘写入策略（仅做网络隔离）、全磁盘读取 + 受限写入、以及完全受限读取 + 受限写入三种模式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `BwrapOptions` | struct | bubblewrap 选项：是否挂载 `/proc`、网络模式 |
| `BwrapNetworkMode` | enum | 网络模式：`FullAccess`（主机网络）、`Isolated`（隔离）、`ProxyOnly`（代理） |
| `BwrapArgs` | struct | 构建结果：命令行参数列表 + 需保留的文件描述符 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_bwrap_command_args` | `pub fn create_bwrap_command_args(command, policy, cwd, command_cwd, options) -> Result<BwrapArgs>` | 入口函数：根据策略生成 bwrap 参数或直接返回原命令 |
| `create_filesystem_args` | `fn create_filesystem_args(policy, cwd) -> Result<BwrapArgs>` | 构建文件系统挂载参数（核心逻辑） |
| `append_unreadable_root_args` | `fn append_unreadable_root_args(args, preserved_files, path, allowed_write_paths)` | 为不可读路径生成 tmpfs 屏蔽或 ro-bind-data 屏蔽参数 |
| `find_symlink_in_path` | `fn find_symlink_in_path(target_path, allowed_write_paths) -> Option<PathBuf>` | 检测路径中的符号链接以防御替换攻击 |
| `find_first_non_existent_component` | `fn find_first_non_existent_component(target_path) -> Option<PathBuf>` | 找到路径中第一个不存在的组件以阻止路径创建 |

## 依赖关系

- **引用的 crate**: `codex_core`, `codex_protocol`, `codex_utils_absolute_path`
- **被引用方**: `linux_run_main.rs`

## 实现备注

- 挂载顺序至关重要：先 ro-bind `/`，再 `--dev /dev`，然后可写根目录的 bind，再覆盖只读子路径的 ro-bind，最后处理不可读路径的 tmpfs 屏蔽
- 不可读目录使用 `--perms 000 --tmpfs + --remount-ro` 组合实现；若有可写子路径则使用 `111` 权限允许遍历
- 不可读文件使用 `--perms 000 --ro-bind-data` + `/dev/null` 文件描述符实现
- 符号链接检测防止攻击者将受保护路径（如 `.codex`）替换为指向可写目录的符号链接
- 包含大量单元测试覆盖各种策略组合场景
