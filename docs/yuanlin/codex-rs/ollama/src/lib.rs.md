# `lib.rs` — Ollama 模块入口

## 功能概述

Ollama 本地 LLM 提供者的入口模块。导出 `OllamaClient` 和 `ensure_oss_ready` 函数。定义默认 OSS 模型为 `qwen3:32b`。`ensure_oss_ready` 验证 Ollama 服务器可达性，检查模型是否本地可用并在需要时执行 pull 下载。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ensure_oss_ready` | `async (config) -> io::Result<()>` | 准备 Ollama OSS 环境 |

## 依赖关系

- **被引用方**: CLI/exec 的 --oss 模式
