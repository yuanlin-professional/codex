# approval-presets

## 功能概述

`codex-utils-approval-presets` 定义了 Codex 系统的内置审批策略预设。每个预设将一个审批策略（`AskForApproval`）和一个沙箱策略（`SandboxPolicy`）打包在一起，提供了面向 UI 的标识符、标签和描述文本。

该 crate 与 UI 无关，可被 TUI 前端和 MCP 服务端共同复用。

## 目录结构

```
approval-presets/
├── Cargo.toml
└── src/
    └── lib.rs          # ApprovalPreset 结构体与内置预设列表
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | 引用 `AskForApproval` 和 `SandboxPolicy` 类型 |

## 核心接口/API

### `ApprovalPreset`

```rust
pub struct ApprovalPreset {
    pub id: &'static str,           // 稳定标识符，如 "read-only"
    pub label: &'static str,        // UI 显示标签，如 "Read Only"
    pub description: &'static str,  // 简短描述
    pub approval: AskForApproval,   // 审批策略
    pub sandbox: SandboxPolicy,     // 沙箱策略
}
```

### `builtin_approval_presets()`

返回内置的三个预设：

| ID | 标签 | 审批策略 | 沙箱策略 |
|----|------|----------|----------|
| `read-only` | Read Only | OnRequest | 只读策略 |
| `auto` | Default | OnRequest | 工作区写入策略 |
| `full-access` | Full Access | Never | 完全访问（危险） |
