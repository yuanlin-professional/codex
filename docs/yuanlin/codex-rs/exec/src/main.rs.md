# `main.rs` — codex-exec 二进制入口

## 功能概述

codex-exec 二进制程序的入口点。支持 arg0 分发机制：当以 `codex-linux-sandbox` 名称调用时，执行沙箱逻辑；正常调用时解析 CLI 参数并启动非交互式 Codex 代理。使用 `TopCli` 包装层合并根级别的 `-c` 配置覆盖到内部 `Cli` 结构体中。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TopCli` | struct | 顶层 CLI 包装，合并 `CliConfigOverrides` 和 `Cli` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `() -> Result<()>` | 入口函数，通过 `arg0_dispatch_or_else` 处理 arg0 分发或正常 CLI 启动 |

## 依赖关系

- **引用的 crate**: `clap`、`codex_arg0`、`codex_exec`、`codex_utils_cli`
- **被引用方**: 无（二进制入口）

## 实现备注

- `TopCli` 将根级别的 `--config` 覆盖 splice 到内部 CLI 的 overrides 列表前端，确保优先级正确。
