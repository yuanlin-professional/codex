# `lib.rs` -- Linux 沙箱 crate 入口

## 功能概述

这是 `codex-linux-sandbox` crate 的库入口文件。在 Linux 平台上，它声明并导出核心沙箱模块（bwrap、landlock、launcher、linux_run_main、proxy_routing、vendored_bwrap），并提供 `run_main()` 作为沙箱辅助进程的统一入口点。在非 Linux 平台上，`run_main()` 会直接 panic。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_main` | `pub fn run_main() -> !` | Linux 上委托给 `linux_run_main::run_main()`；非 Linux 上 panic |

## 依赖关系

- **引用的 crate**: 无外部依赖（仅条件编译内部模块）
- **被引用方**: `main.rs`（二进制入口）

## 实现备注

所有内部模块均以 `#[cfg(target_os = "linux")]` 条件编译门控，确保在非 Linux 平台上不引入系统特有依赖。
