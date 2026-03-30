# `approval_mode_cli_arg.rs` — CLI 审批模式参数类型

## 功能概述

定义 `ApprovalModeCliArg` 枚举，作为 `--approval-mode` CLI 选项的标准类型。支持四种模式：`Untrusted`（仅运行受信命令）、`OnFailure`（已弃用）、`OnRequest`（模型决定何时请求审批）和 `Never`（从不请求审批）。通过 `From` trait 转换为协议层的 `AskForApproval` 类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApprovalModeCliArg` | enum | CLI 审批模式：`Untrusted`, `OnFailure`, `OnRequest`, `Never` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `From<ApprovalModeCliArg> for AskForApproval` | impl | 将 CLI 参数转换为协议审批策略 |

## 依赖关系

- **引用的 crate**: `clap`, `codex_protocol`
- **被引用方**: CLI 入口（`cli/src/lib.rs`）
