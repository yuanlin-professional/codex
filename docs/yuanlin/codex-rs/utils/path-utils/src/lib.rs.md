# `lib.rs` — 路径规范化、符号链接解析与原子写入

## 功能概述

提供路径处理核心工具：`normalize_for_path_comparison` 通过 canonicalize 并处理 WSL 大小写问题来规范化路径用于比较；`resolve_symlink_write_paths` 跟踪符号链接链（含环检测）找到最终写入路径；`write_atomically` 使用临时文件实现原子性文件写入。WSL 模式下 `/mnt/<drive>/...` 路径会被小写化以匹配 Windows 的大小写不敏感语义。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SymlinkWritePaths` | struct | 包含 `read_path` 和 `write_path` 的符号链接解析结果 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `normalize_for_path_comparison` | `fn(path) -> io::Result<PathBuf>` | 规范化路径用于比较 |
| `normalize_for_native_workdir` | `fn(path) -> PathBuf` | Windows 下简化 verbatim 路径 |
| `resolve_symlink_write_paths` | `fn(path) -> io::Result<SymlinkWritePaths>` | 解析符号链接到最终写入目标 |
| `write_atomically` | `fn(write_path, contents) -> io::Result<()>` | 原子性写入文件 |

## 依赖关系

- **引用的 crate**: `codex_utils_absolute_path`, `tempfile`, `dunce`
- **被引用方**: 文件操作模块

## 实现备注

- 符号链接解析使用 visited 集合检测循环，无固定最大深度。
- Windows 使用 `dunce::simplified` 移除 verbatim 前缀（`\\?\`）。
