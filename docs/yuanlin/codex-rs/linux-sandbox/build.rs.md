# `build.rs` -- 构建脚本：编译内嵌 bubblewrap

## 功能概述

这是 `codex-linux-sandbox` crate 的 Cargo 构建脚本。它负责在 Linux 目标平台上将 bubblewrap 的 C 源代码编译为静态库，并通过 FFI 暴露 `bwrap_main` 符号供 Rust 侧调用。在非 Linux 平台上构建时直接跳过。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `fn main()` | 构建脚本入口，设置 `rerun-if-changed` 和 `check-cfg`，Linux 上调用 `try_build_vendored_bwrap` |
| `try_build_vendored_bwrap` | `fn try_build_vendored_bwrap() -> Result<(), String>` | 查找 bubblewrap 源码目录，使用 `cc` crate 编译 C 源文件，链接 `libcap`，设置 `vendored_bwrap_available` cfg |
| `resolve_bwrap_source_dir` | `fn resolve_bwrap_source_dir(manifest_dir: &Path) -> Result<PathBuf, String>` | 按优先级解析 bubblewrap 源码目录：1) `CODEX_BWRAP_SOURCE_DIR` 环境变量 2) `codex-rs/vendor/bubblewrap` |

## 依赖关系

- **引用的 crate**: `cc` (C 编译器), `pkg_config` (查找 libcap)
- **被引用方**: Cargo 构建系统自动调用

## 实现备注

- 通过 `#define main bwrap_main` 将 bubblewrap 的 `main` 函数重命名，以便 Rust 通过 FFI 调用
- 使用 `-idirafter` 标志确保 musl 交叉编译时目标 sysroot 头文件优先
- 生成的 `config.h` 提供 bubblewrap 所需的 `PACKAGE_STRING` 宏定义
