# `lib.rs` — 开源模型提供商工具

## 功能概述

提供 OSS（开源模型）提供商的共享工具函数，供 TUI 和 exec 模块使用。`get_default_model_for_oss_provider` 根据提供商 ID 返回默认模型名；`ensure_oss_provider_ready` 异步检查指定提供商的就绪状态（模型已下载、服务可达）。当前支持 LMStudio 和 Ollama 两个提供商。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_default_model_for_oss_provider` | `fn(provider_id: &str) -> Option<&'static str>` | 获取提供商的默认模型 |
| `ensure_oss_provider_ready` | `async fn(provider_id, config) -> Result<(), io::Error>` | 确保提供商就绪 |

## 依赖关系

- **引用的 crate**: `codex_core`, `codex_lmstudio`, `codex_ollama`
- **被引用方**: TUI 和 exec 启动流程

## 实现备注

- 未知提供商 ID 在 `get_default_model_for_oss_provider` 中返回 `None`，在 `ensure_oss_provider_ready` 中静默跳过。
