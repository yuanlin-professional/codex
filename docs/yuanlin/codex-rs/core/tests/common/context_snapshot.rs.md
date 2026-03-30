# `context_snapshot.rs` — 测试快照格式化工具

## 功能概述

提供用于集成测试的"上下文快照"格式化工具，能将 Responses API 请求的 `input` 数组渲染为可读的快照字符串，支持多种渲染模式（完整文本、脱敏文本、仅类型、带前缀截断）。这些快照用于 `insta` 快照测试，使测试断言更清晰且不受动态内容（如临时路径、权限指令等）影响。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ContextSnapshotRenderMode` | enum | 渲染模式枚举：`RedactedText`（脱敏，默认）、`FullText`（完整）、`KindOnly`（仅类型）、`KindWithTextPrefix { max_chars }` （类型+前缀截断） |
| `ContextSnapshotOptions` | struct | 快照选项配置，包含渲染模式、是否剥离能力指令、是否剥离 AGENTS.md 用户上下文 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_request_input_snapshot` | `fn(request: &ResponsesRequest, options: &ContextSnapshotOptions) -> String` | 从 `ResponsesRequest` 提取 `input` 并格式化为快照字符串 |
| `format_response_items_snapshot` | `fn(items: &[Value], options: &ContextSnapshotOptions) -> String` | 将 JSON `input` 数组逐项渲染为 `{idx:02}:{type}/{detail}` 格式的快照文本 |
| `format_labeled_requests_snapshot` | `fn(scenario: &str, sections: &[(&str, &ResponsesRequest)], ...) -> String` | 渲染带标签的多段请求快照，用于多步骤场景 |
| `format_labeled_items_snapshot` | `fn(scenario: &str, sections: &[(&str, &[Value])], ...) -> String` | 类似上一个，但直接接受 `Value` 数组 |
| `canonicalize_snapshot_text` | `fn(text: &str) -> String` | 将权限指令、Apps 指令、环境上下文、AGENTS.md 等内容替换为占位符（如 `<PERMISSIONS_INSTRUCTIONS>`、`<ENVIRONMENT_CONTEXT:cwd=<CWD>>` 等） |

## 依赖关系

- **引用的 crate**: `regex_lite`、`serde_json`、`codex_protocol::protocol`
- **被引用方**: 被各集成测试中使用 `insta` 快照比较时调用

## 实现备注

- `RedactedText` 模式下会将 `<environment_context>` 中的 `<cwd>` 和 `<subagents>` 提取并规范化为 `<ENVIRONMENT_CONTEXT:cwd=<CWD>:subagents=N>` 格式
- `normalize_dynamic_snapshot_paths()` 使用正则将系统技能的临时路径（如 `/private/var/folders/.../skills/.system/openai-docs/SKILL.md`）替换为 `<SYSTEM_SKILLS_ROOT>/openai-docs/SKILL.md`
- 模块内含 `mod tests` 单元测试，覆盖各渲染模式、CRLF 规范化、能力指令剥离、图片内容渲染等场景
