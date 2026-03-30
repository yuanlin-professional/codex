# `vendored_bwrap.rs` -- 内嵌 bubblewrap FFI 封装

## 功能概述

本模块封装了构建时编译的 bubblewrap C 代码的 FFI 接口。当 `vendored_bwrap_available` cfg 标志启用时（由 `build.rs` 设置），它通过 `extern "C"` 调用编译后的 `bwrap_main` 函数。当该标志未启用时（非 Linux 平台或编译失败），所有调用会 panic 并给出明确的错误消息。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_vendored_bwrap_main` | `pub fn run_vendored_bwrap_main(argv: &[String], preserved_files: &[File]) -> c_int` | 调用内嵌 bubblewrap 的 main 函数并返回退出码 |
| `exec_vendored_bwrap` | `pub fn exec_vendored_bwrap(argv: Vec<String>, preserved_files: Vec<File>) -> !` | 运行内嵌 bubblewrap 并以其退出码终止进程 |

## 依赖关系

- **引用的 crate**: `libc`, `std::ffi::CString`
- **被引用方**: `launcher.rs`

## 实现备注

- 成功时 bubblewrap 会执行 `execve` 系统调用切换到目标程序，因此函数不会返回
- `preserved_files` 参数用于保持文件描述符存活（如 seccomp BPF 数据），但内嵌路径中实际不需要特殊处理
