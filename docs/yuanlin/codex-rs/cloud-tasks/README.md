# cloud-tasks

## 功能概述

`codex-cloud-tasks` 是 Codex 项目的云任务管理模块，提供与 Codex 云端任务系统交互的命令行界面。它支持创建云端编码任务、查看任务列表和状态、查看代码差异（diff）以及将任务结果应用到本地仓库。该模块包含完整的 TUI（终端用户界面），基于 `ratatui` 构建交互式界面。

## 架构说明

该 crate 采用经典的 CLI/TUI 应用架构：

### 1. cli (Cli)
基于 `clap` 的命令行参数定义，作为程序入口。

### 2. app
应用主逻辑，协调后端 API 调用、状态管理和用户交互。

### 3. ui
基于 `ratatui` + `crossterm` 的终端 UI 渲染。

### 4. new_task
新任务创建逻辑。

### 5. scrollable_diff
可滚动的 diff 查看器，用于展示代码变更。

### 6. env_detect
环境检测，确定运行上下文。

### 7. util
工具函数集（错误日志、时间格式化、User-Agent 后缀设置等）。

### 后端交互

```
CLI 参数 -> init_backend() -> BackendContext
                                  |
              codex-cloud-tasks-client (MockClient / HttpClient)
                                  |
                     ChatGPT Backend API (https://chatgpt.com/backend-api)
```

支持 Mock 模式（通过 `CODEX_CLOUD_TASKS_MODE=mock` 环境变量启用）。

## 目录结构

```
cloud-tasks/
  Cargo.toml
  src/
    lib.rs              # 模块入口，后端初始化
    cli.rs              # CLI 参数定义
    app.rs              # 应用主逻辑
    ui.rs               # TUI 渲染
    new_task.rs         # 新任务创建
    scrollable_diff.rs  # 可滚动 diff 查看器
    env_detect.rs       # 环境检测
    util.rs             # 工具函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-cloud-tasks-client` | 云任务后端客户端（mock + online） |
| `codex-core` | Codex 核心（默认 HTTP 客户端） |
| `codex-client` | Codex 客户端 |
| `codex-git-utils` | Git 操作（分支名、默认分支等） |
| `codex-login` | 认证登录 |
| `codex-tui` | TUI 共享组件 |
| `ratatui` / `crossterm` | 终端 UI 框架 |
| `tokio` / `tokio-stream` | 异步运行时 |
| `reqwest` | HTTP 客户端 |
| `chrono` | 时间处理 |
| `clap` | 命令行参数 |
| `serde` / `serde_json` | 序列化 |
| `owo-colors` / `supports-color` | 终端颜色 |
| `unicode-width` | Unicode 宽度计算 |
| `tracing` / `tracing-subscriber` | 日志 |
| `async-trait` | 异步 trait |

## 核心接口/API

### CLI 入口

- **`Cli`** - 命令行参数结构体（`pub use cli::Cli`）

### 环境变量

| 变量 | 用途 |
|------|------|
| `CODEX_CLOUD_TASKS_MODE` | 设为 `"mock"` 启用模拟后端 |
| `CODEX_CLOUD_TASKS_BASE_URL` | 后端 API 地址（默认 `https://chatgpt.com/backend-api`） |

### 公开模块

- **`env_detect`** - 环境检测工具
- **`scrollable_diff`** - 可滚动 diff 查看器组件
- **`util`** - 工具函数（错误日志追加、相对时间格式化、User-Agent 设置）
