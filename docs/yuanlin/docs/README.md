# docs — 项目文档目录

## 功能概述

`docs/` 是 Codex 项目的文档目录，包含用户指南、开发者文档、设计文档和贡献指南。大部分文档以 Markdown 格式编写，托管在 GitHub 仓库中，并被官方文档站点 `developers.openai.com/codex` 引用。

## 目录结构

| 文件 | 说明 |
|------|------|
| `getting-started.md` | 入门指南 — 提示词用法、快捷键、会话管理 |
| `config.md` | 配置指南 — config.toml 详细配置选项 |
| `install.md` | 安装指南 — 各平台安装方法 |
| `contributing.md` | 贡献指南 — 开发环境搭建、PR 流程 |
| `authentication.md` | 认证文档 |
| `exec.md` | exec 模式文档 |
| `execpolicy.md` | 执行策略文档 |
| `sandbox.md` | 沙箱机制文档 |
| `skills.md` | 技能系统文档 |
| `slash_commands.md` | 斜杠命令文档 |
| `agents_md.md` | AGENTS.md 格式说明 |
| `example-config.md` | 配置示例 |
| `CLA.md` | 贡献者许可协议 |
| `open-source-fund.md` | 开源基金说明 |
| `license.md` | 许可证说明 |
| `js_repl.md` | JavaScript REPL 设计文档 |
| `exit-confirmation-prompt-design.md` | 退出确认提示设计文档 |
| `tui-alternate-screen.md` | TUI 备用屏幕设计 |
| `tui-chat-composer.md` | TUI 聊天输入框设计 |
| `tui-request-user-input.md` | TUI 用户输入请求设计 |
| `tui-stream-chunking-*.md` | TUI 流式分块相关文档（review/tuning/validation） |
| `yuanlin/` | 项目中文文档目录（本文档所在位置） |

## 依赖关系

### 内部引用
- 被项目根 `README.md` 引用
- 被 `codex-rs/README.md` 引用
- 被官方文档站点 `developers.openai.com/codex` 引用
- TUI 设计文档对应 `codex-rs/tui/` 的实现
