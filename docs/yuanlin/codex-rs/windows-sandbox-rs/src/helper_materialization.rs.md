# `helper_materialization.rs` -- 沙箱辅助可执行文件的物化与缓存

## 功能概述

该文件管理沙箱辅助可执行文件（如 `codex-command-runner.exe`）从源位置到沙箱 bin 目录（`.sandbox-bin`）的复制和缓存。它确保运行器二进制文件在沙箱 bin 目录中存在且是最新的，使用文件大小和修改时间判断是否需要重新复制。通过内存缓存（`OnceLock<Mutex<HashMap>>`）避免重复的文件系统操作。复制操作使用临时文件+重命名模式以保证原子性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HelperExecutable` | enum | 辅助可执行文件类型枚举，目前仅包含 `CommandRunner` |
| `CopyOutcome` | enum | 复制结果：`Reused`（已是最新）或 `ReCopied`（重新复制） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_helper_for_launch` | `fn(kind, codex_home, log_dir) -> PathBuf` | 解析辅助文件路径，优先使用复制版本，失败回退到 legacy 路径 |
| `resolve_current_exe_for_launch` | `fn(codex_home, fallback_executable) -> PathBuf` | 将当前可执行文件复制到沙箱 bin 目录 |
| `copy_helper_if_needed` | `fn(kind, codex_home, log_dir) -> Result<PathBuf>` | 按需复制辅助文件，带内存缓存 |
| `copy_from_source_if_needed` | `fn(source, destination) -> Result<CopyOutcome>` | 检查目标是否最新，不最新则通过临时文件复制 |
| `destination_is_fresh` | `fn(source, destination) -> Result<bool>` | 比较源和目标的文件大小和修改时间 |
| `legacy_lookup` | `fn(kind) -> PathBuf` | 在当前可执行文件同级目录查找辅助文件 |
| `helper_bin_dir` | `fn(codex_home: &Path) -> PathBuf` | 返回 `.sandbox-bin` 目录路径 |

## 依赖关系

- **引用的 crate**: `anyhow`, `tempfile`, `crate::logging`, `crate::sandbox_bin_dir`
- **被引用方**: `runner_pipe.rs`, `elevated_impl.rs`

## 实现备注

- 使用 `NamedTempFile` + `fs::rename` 确保复制操作的原子性
- 复制到临时文件后再重命名，保持目标目录的继承 ACL
- 包含 5 个测试覆盖：缺失目标的复制、freshness 判断、复用最新目标、bin 目录路径、runner 复制
