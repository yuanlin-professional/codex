# `cwd_junction.rs` -- 沙箱工作目录 Junction 管理

## 功能概述

该文件实现了为 Windows 沙箱用户创建 NTFS junction（目录连接点）的功能，将请求的工作目录（CWD）映射到沙箱用户配置文件下的一个稳定路径。这在 ACL 辅助程序（read ACL mutex）运行时需要使用，因为直接访问原始工作目录可能受到权限限制。Junction 通过 `cmd /c mklink /J` 命令创建，路径基于原始 CWD 的哈希值命名以保证唯一性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件无公共结构体定义 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_cwd_junction` | `fn(requested_cwd: &Path, log_dir: Option<&Path>) -> Option<PathBuf>` | 创建或复用指向请求 CWD 的 junction，返回 junction 路径 |
| `junction_name_for_path` | `fn(path: &Path) -> String` | 根据路径哈希生成 junction 名称 |
| `junction_root_for_userprofile` | `fn(userprofile: &str) -> PathBuf` | 返回 junction 存储根目录 `USERPROFILE/.codex/.sandbox/cwd` |

## 依赖关系

- **引用的 crate**: `windows_sys`（`FILE_ATTRIBUTE_REPARSE_POINT`），`codex_windows_sandbox`（`log_note`）
- **被引用方**: `command_runner_win.rs` 中的 `effective_cwd` 函数

## 实现备注

- 仅在 `target_os = "windows"` 下编译
- 使用 `raw_arg` 传递参数给 `cmd.exe`，避免 `std::process::Command::args()` 的自动引号处理导致 mklink 语法错误
- 复用现有 junction 时检查 `FILE_ATTRIBUTE_REPARSE_POINT` 属性以验证确实是 junction
- Windows 路径不允许包含引号，因此无需额外转义
