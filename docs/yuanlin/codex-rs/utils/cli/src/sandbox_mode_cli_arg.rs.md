# `sandbox_mode_cli_arg.rs` — CLI 沙箱模式参数类型

## 功能概述

定义 `SandboxModeCliArg` 枚举，作为 `--sandbox`（`-s`）CLI 选项的标准类型。提供三种模式：`ReadOnly`、`WorkspaceWrite` 和 `DangerFullAccess`，不含关联数据以便作为简单命令行标志使用。通过 `From` trait 转换为配置层的 `SandboxMode` 类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxModeCliArg` | enum | CLI 沙箱模式枚举 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `From<SandboxModeCliArg> for SandboxMode` | impl | 将 CLI 参数转换为配置沙箱模式 |

## 依赖关系

- **引用的 crate**: `clap`, `codex_protocol`
- **被引用方**: CLI 入口模块

## 实现备注

- 需要高级 `workspace-write` 选项的用户可通过 `-c` 覆盖或 `config.toml` 配置。
