# `main.rs` — Codex CLI 二进制入口

## 功能概述

Codex 主 CLI 二进制的入口点。通过 arg0 分发机制支持多种调用模式，解析 CLI 参数并启动交互式 TUI 或 app-server 子命令。

## 依赖关系

- **引用的 crate**: `codex_arg0`、`codex_cli`、`clap`
