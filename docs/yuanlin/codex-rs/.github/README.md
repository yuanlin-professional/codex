# codex-rs/.github -- Rust 工作空间 GitHub 配置

## 功能概述

`codex-rs/.github/` 目录包含专门针对 Codex Rust 工作空间（codex-rs）的 GitHub Actions 工作流。这些工作流独立于项目根目录的 `.github/workflows/`，专注于 Rust 依赖的安全审计。

## 目录结构

```
codex-rs/.github/
└── workflows/
    └── cargo-audit.yml    # Cargo 依赖安全审计工作流
```

## 工作流说明

### cargo-audit.yml

**名称**：Cargo audit

**触发条件**：
- Pull Request 事件
- Push 到 main 分支

**执行内容**：
1. Checkout 代码
2. 安装 Rust stable 工具链
3. 安装 cargo-audit 工具
4. 在 `codex-rs/` 目录下运行 `cargo audit --deny warnings`

**权限**：仅需 contents:read

**作用**：自动检查 Rust 依赖中的已知安全漏洞，在 PR 和 main 合并时提供安全门禁。已知的误报或无法修复的警告在 `codex-rs/.cargo/audit.toml` 中配置忽略。

## 依赖关系

- **外部 Actions**：actions/checkout@v4、dtolnay/rust-toolchain@stable、taiki-e/install-action@v2
- **配置文件**：`codex-rs/.cargo/audit.toml`（审计例外规则）
- **审计范围**：`codex-rs/Cargo.lock` 中的所有依赖
