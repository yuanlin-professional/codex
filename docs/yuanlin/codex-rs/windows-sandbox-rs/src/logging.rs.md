# `logging.rs` -- Windows 沙箱日志记录工具模块

## 功能概述

该文件提供沙箱执行过程中的日志记录工具函数。日志以 `sandbox.log` 文件名写入指定的基础目录，支持命令预览截断（限制 200 字节以避免过长输出）、启动/成功/失败记录、调试日志（通过 `SBX_DEBUG=1` 环境变量开启）以及通用注释日志。日志行带有时间戳和进程标签前缀。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LOG_FILE_NAME` | const | 日志文件名常量 `"sandbox.log"` |
| `LOG_COMMAND_PREVIEW_LIMIT` | const | 命令预览字符数限制（200） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `log_start` | `fn(command: &[String], base_dir: Option<&Path>)` | 记录命令开始执行 |
| `log_success` | `fn(command: &[String], base_dir: Option<&Path>)` | 记录命令执行成功 |
| `log_failure` | `fn(command: &[String], detail: &str, base_dir: Option<&Path>)` | 记录命令执行失败及详情 |
| `debug_log` | `fn(msg: &str, base_dir: Option<&Path>)` | 调试日志，仅当 `SBX_DEBUG=1` 时输出 |
| `log_note` | `fn(msg: &str, base_dir: Option<&Path>)` | 无条件写入带时间戳的日志行 |
| `preview` | `fn(command: &[String]) -> String` | 将命令数组拼接并截断至安全的 UTF-8 边界 |

## 依赖关系

- **引用的 crate**: `chrono`, `codex_utils_string`（`take_bytes_at_char_boundary`）
- **被引用方**: `elevated_impl.rs`, `elevated/command_runner_win.rs` 等沙箱执行模块

## 实现备注

- 使用 `OnceLock` 缓存当前可执行文件名作为日志标签
- `preview` 函数使用 `take_bytes_at_char_boundary` 确保在 UTF-8 多字节字符边界处安全截断
- 包含测试确保 4 字节 emoji 在截断边界处不会 panic
