# codex-cli — 旧版 TypeScript CLI

## 功能概述

`codex-cli/` 是 Codex CLI 的**旧版 TypeScript 实现**，已被 `codex-rs/` 中的 Rust 实现取代。该目录保留用于历史参考和 npm 包发布兼容。

当前该目录主要作为 npm 包 `@openai/codex` 的入口包装器——`bin/codex.js` 实际上会委托给 Rust 编译的原生二进制文件。

### 原有功能（已迁移至 Rust）
- 交互式终端代码代理
- 多模型提供商支持（OpenAI、Azure、自定义）
- 沙箱化命令执行
- 项目记忆和文档
- 非交互式 CI 模式
- 配置管理（config.json，现改为 config.toml）

## 目录结构

| 文件/目录 | 说明 |
|-----------|------|
| `bin/codex.js` | CLI 入口脚本，委托给 Rust 二进制 |
| `scripts/` | 构建和发布脚本 |
| `package.json` | npm 包配置（`@openai/codex`） |
| `README.md` | 旧版详细文档（含完整使用指南） |
| `Dockerfile` | Docker 构建配置 |

## 依赖关系

### 外部依赖
- Node.js >= 16（运行时要求）
- pnpm 包管理器

### 内部关系
- 现在仅作为 Rust 二进制的 npm 分发包装器
- 实际 CLI 功能由 `codex-rs/cli/` 提供
