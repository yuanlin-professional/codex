# `lib.rs` — 审批策略预设定义

## 功能概述

本文件定义了 `ApprovalPreset` 结构体和内置审批预设列表函数 `builtin_approval_presets`。每个预设将审批策略（`AskForApproval`）与沙箱策略（`SandboxPolicy`）配对，供 TUI 和 MCP 服务器等前端 UI 复用。预设包括 "Read Only"、"Default"（Agent 模式）和 "Full Access" 三种级别。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApprovalPreset` | struct | 包含 id、label、description、approval、sandbox 字段的预设 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `builtin_approval_presets` | `fn() -> Vec<ApprovalPreset>` | 返回内置审批预设列表 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`AskForApproval`, `SandboxPolicy`）
- **被引用方**: TUI 前端、MCP 服务器等需要展示审批选项的组件

## 实现备注

- 预设设计为 UI 无关，便于跨前端复用。
