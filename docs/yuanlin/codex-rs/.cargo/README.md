# codex-rs/.cargo -- Cargo 构建配置

## 功能概述

`codex-rs/.cargo/` 目录包含 Cargo（Rust 构建系统）的工作空间级配置文件，主要用于设置平台特定的编译和链接参数，以及安全审计的例外规则。

## 目录结构

```
codex-rs/.cargo/
├── config.toml    # Cargo 构建配置（链接参数）
└── audit.toml     # cargo-audit 安全审计配置
```

## 配置说明

### config.toml -- 构建配置

针对 Windows 平台设置了栈大小和链接参数：

| 目标平台 | 配置 |
|----------|------|
| `cfg(all(windows, target_env = "msvc"))` | MSVC 链接参数：`/STACK:8388608`（8MB 栈大小） |
| `aarch64-pc-windows-msvc` | 在 8MB 栈大小基础上额外添加 `/arm64hazardfree` 以禁用 Cortex-A53 #843419 警告 |
| `cfg(all(windows, target_env = "gnu"))` | GNU 链接参数：`-Wl,--stack,8388608`（8MB 栈大小） |

### audit.toml -- 安全审计例外

配置 `cargo audit` 忽略的已知安全公告：

| 公告编号 | 说明 |
|----------|------|
| `RUSTSEC-2024-0388` | derivative 2.2.0（经 starlark 引入），上游 crate 已停止维护 |
| `RUSTSEC-2025-0057` | fxhash 0.2.1（经 starlark_map 引入），上游 crate 已停止维护 |
| `RUSTSEC-2024-0436` | paste 1.0.15（经 starlark/ratatui 引入），上游 crate 已停止维护 |

## 依赖关系

- **影响范围**：整个 codex-rs Cargo 工作空间的构建行为
- **被引用方**：所有 `cargo build`、`cargo test`、`cargo clippy` 等命令自动读取
- **相关工作流**：`cargo-deny.yml`、`codex-rs/.github/workflows/cargo-audit.yml`
