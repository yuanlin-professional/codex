# `cli.rs` — codex-exec 命令行参数定义

## 功能概述

该文件使用 `clap` 框架定义了 `codex-exec` 二进制的完整命令行接口。顶层结构体 `Cli` 包含模型选择、沙箱策略、OSS 提供者、颜色模式、JSON 输出等选项。支持两个子命令：`resume`（恢复先前会话）和 `review`（执行代码审查）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Cli` | struct | 顶层 CLI 参数，包含模型、沙箱、颜色、JSON 模式等所有选项 |
| `Command` | enum | 子命令枚举：`Resume` 和 `Review` |
| `ResumeArgs` | struct | 恢复会话的参数，包含 session_id、last 标志、prompt 等 |
| `ResumeArgsRaw` | struct (内部) | clap 原始解析形式，通过 `From` 转换为 `ResumeArgs` 处理条件位置参数 |
| `ReviewArgs` | struct | 代码审查参数：--uncommitted、--base、--commit、自定义 prompt |
| `Color` | enum | 颜色输出模式：Always、Never、Auto |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `From<ResumeArgsRaw> for ResumeArgs` | `fn from(raw) -> Self` | 当 `--last` 且无 prompt 时，将位置参数重新解释为 prompt 而非 session_id |

## 依赖关系

- **引用的 crate**: `clap`、`codex_utils_cli`（`CliConfigOverrides`、`SandboxModeCliArg`）
- **被引用方**: `exec/src/lib.rs`（主运行逻辑）、`exec/src/main.rs`

## 实现备注

- `--dangerously-bypass-approvals-and-sandbox` 有别名 `--yolo`，与 `--full-auto` 互斥。
- `ResumeArgs` 通过内部 `ResumeArgsRaw` 实现条件参数语义：当 `--last` 被指定且无显式 prompt 时，位置参数被当作 prompt 而非 session id。
