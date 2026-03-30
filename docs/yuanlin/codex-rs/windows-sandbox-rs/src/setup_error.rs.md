# `setup_error.rs` — 沙盒安装错误码与报告机制

## 功能概述
该文件定义了 Windows 沙盒安装过程中可能出现的所有错误码、错误报告结构和辅助函数。错误码分为编排器（Orchestrator）和辅助程序（Helper）两类，用于诊断安装失败的具体阶段。还提供了错误报告的读写、清除以及错误消息脱敏（移除用户名）等功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SetupErrorCode` | enum | 安装错误码枚举（约25种） |
| `SetupErrorReport` | struct | 包含错误码和消息的报告 |
| `SetupFailure` | struct | 可作为 anyhow Error 使用的安装失败类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_setup_error_report` | `(codex_home, report) -> Result<()>` | 写入错误报告 JSON |
| `read_setup_error_report` | `(codex_home) -> Result<Option<Report>>` | 读取错误报告 |
| `clear_setup_error_report` | `(codex_home) -> Result<()>` | 清除错误报告 |
| `sanitize_setup_metric_tag_value` | `(value) -> String` | 脱敏错误消息中的用户名 |

## 依赖关系
- **引用的 crate**: `anyhow`, `serde`, `codex_utils_string`
- **被引用方**: `setup_main_win`, `setup_orchestrator`
