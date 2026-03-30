# `lib.rs` — 测试二进制与资源定位工具

## 功能概述

本文件提供在 `cargo test` 和 `bazel test` 两种构建系统下透明定位测试二进制文件和资源文件的辅助函数。`cargo_bin` 函数通过 `CARGO_BIN_EXE_*` 环境变量或 `assert_cmd` 回退来查找可执行文件。`find_resource!` 宏在 Cargo 和 Bazel 两种环境下自动解析资源路径。此外还提供 `repo_root` 函数用于定位仓库根目录。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CargoBinError` | enum | 二进制定位错误：`CurrentExe`、`CurrentDir`、`ResolvedPathDoesNotExist`、`NotFound` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `cargo_bin` | `fn(name: &str) -> Result<PathBuf, CargoBinError>` | 定位当前测试运行中构建的二进制文件 |
| `runfiles_available` | `fn() -> bool` | 检查是否在 Bazel runfiles 模式下运行 |
| `resolve_bazel_runfile` | `fn(bazel_package, resource) -> io::Result<PathBuf>` | 解析 Bazel runfile 路径 |
| `resolve_cargo_runfile` | `fn(resource) -> io::Result<PathBuf>` | 解析 Cargo manifest 相对资源路径 |
| `repo_root` | `fn() -> io::Result<PathBuf>` | 定位仓库根目录 |
| `find_resource!` | macro | 在 Cargo/Bazel 环境下自动解析资源路径 |

## 依赖关系

- **引用的 crate**: `assert_cmd`, `runfiles`, `thiserror`
- **被引用方**: 项目中的集成测试代码

## 实现备注

- Bazel 使用 `RUNFILES_MANIFEST_ONLY` 环境变量标识运行模式。
- `normalize_runfile_path` 处理路径中的 `.` 和 `..` 组件。
- Cargo 使用 `CARGO_BIN_EXE_*` 环境变量，并自动处理名称中 `-` 到 `_` 的替换。
