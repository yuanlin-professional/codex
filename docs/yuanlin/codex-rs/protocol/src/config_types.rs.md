# `config_types.rs` — 配置类型定义

## 功能概述

定义了 Codex 会话中所有可配置选项的枚举与结构体类型，包括推理摘要模式、输出详细度、沙箱模式、审批审阅者、Windows 沙箱级别、人格风格、网页搜索配置、服务层级、登录方式、信任级别、终端备用屏幕模式、协作模式及其遮罩(mask)等。这些类型在配置文件解析、CLI 参数处理、TUI 渲染以及 API 请求/响应中广泛使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ReasoningSummary` | 枚举 | 推理摘要模式（Auto / Concise / Detailed / None） |
| `Verbosity` | 枚举 | 输出详细度（Low / Medium / High） |
| `SandboxMode` | 枚举 | 沙箱模式（ReadOnly / WorkspaceWrite / DangerFullAccess） |
| `ApprovalsReviewer` | 枚举 | 审批审阅者（User / GuardianSubagent） |
| `Personality` | 枚举 | 人格风格（None / Friendly / Pragmatic） |
| `WebSearchMode` | 枚举 | 网页搜索模式（Disabled / Cached / Live） |
| `WebSearchToolConfig` | 结构体 | 网页搜索工具配置（上下文大小、域名限制、地理位置） |
| `ModeKind` | 枚举 | 协作模式类型（Plan / Default / PairProgramming / Execute） |
| `CollaborationMode` | 结构体 | 完整协作模式，包含模式类型和设置 |
| `CollaborationModeMask` | 结构体 | 协作模式的部分更新遮罩 |
| `AltScreenMode` | 枚举 | TUI 备用屏幕缓冲区策略（Auto / Always / Never） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_updates` | `fn with_updates(&self, ...) -> Self` | 用新值更新协作模式的模型/effort/指令 |
| `apply_mask` | `fn apply_mask(&self, mask: &CollaborationModeMask) -> Self` | 应用部分遮罩生成新的协作模式 |
| `WebSearchLocation::merge` | `fn merge(&self, other: &Self) -> Self` | 合并两个地理位置配置，overlay 优先 |
| `WebSearchToolConfig::merge` | `fn merge(&self, other: &Self) -> Self` | 合并两个搜索工具配置 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `strum_macros`, `ts_rs`
- **被引用方**: `models.rs`, `protocol.rs`, `app-server-protocol` 中的 v2 类型映射

## 实现备注

- `ModeKind` 使用 `#[serde(alias = ...)]` 使 `code`、`pair_programming`、`execute`、`custom` 全部反序列化为 `Default` 变体。
- `AltScreenMode` 的文档详细解释了为何需要此配置：Zellij 终端复用器在备用屏幕模式下禁用滚动回退。
- 包含合并逻辑和模式遮罩的完整测试，确保 overlay 值优先、可清空可选字段。
