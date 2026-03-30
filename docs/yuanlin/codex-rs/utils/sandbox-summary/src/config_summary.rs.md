# `config_summary.rs` — 配置摘要生成

## 功能概述

构建配置的键值对摘要列表，包含工作目录、模型、提供商、审批策略、沙箱策略等信息。当使用 Responses wire API 时额外包含推理努力级别和推理摘要设置。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_config_summary_entries` | `fn(config: &Config, model: &str) -> Vec<(&'static str, String)>` | 生成配置摘要键值对列表 |

## 依赖关系

- **引用的 crate**: `codex_core`
- **被引用方**: TUI 启动信息展示
