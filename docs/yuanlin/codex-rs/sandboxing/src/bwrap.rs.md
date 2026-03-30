# `bwrap.rs` — Bubblewrap (bwrap) 系统工具检测

## 功能概述

负责在 Linux 系统上检测系统级 Bubblewrap (`bwrap`) 沙箱工具的可用性。通过搜索 PATH 环境变量中的目录来查找 `bwrap` 可执行文件，但会排除工作目录下的本地 `bwrap`（防止工作区注入攻击）。如果找不到系统 bwrap，生成一条警告信息提示用户安装，同时通知 Codex 将使用内置的 bwrap 版本。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| (无) | - | 本模块仅包含函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `system_bwrap_warning` | `() -> Option<String>` | 检查系统 bwrap 可用性，不可用时返回警告信息 |
| `find_system_bwrap_in_path` | `() -> Option<PathBuf>` | 在 PATH 中搜索系统级 bwrap 可执行文件 |
| `find_system_bwrap_in_search_paths` | `(search_paths, cwd) -> Option<PathBuf>` | 内部实现：在指定搜索路径中查找 bwrap，排除 cwd 下的版本 |

## 依赖关系

- **引用的 crate**: `which`
- **被引用方**: `lib.rs`（条件编译导出，仅 Linux）

## 实现备注

- 仅在 `target_os = "linux"` 时编译。
- 使用 `which::which_in_all` 搜索可执行文件，并通过 `canonicalize` 确保路径唯一性。
- 安全考量：排除 cwd 目录下的 bwrap，防止工作区中植入的恶意 bwrap 被使用。
