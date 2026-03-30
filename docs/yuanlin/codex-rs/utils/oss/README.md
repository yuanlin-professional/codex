# oss

## 功能概述

`codex-utils-oss` 提供了开源模型提供商（OSS Provider）的共享工具函数，供 TUI 和执行层共同使用。目前支持两个 OSS 提供商：
- **LM Studio** -- 本地 LLM 推理引擎
- **Ollama** -- 本地 LLM 运行平台

主要功能包括获取提供商的默认模型名称，以及确保提供商服务就绪（模型已下载、服务可达）。

## 目录结构

```
oss/
├── Cargo.toml
└── src/
    └── lib.rs          # OSS 提供商工具函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | OSS 提供商 ID 常量和 Config 类型 |
| `codex-lmstudio` | LM Studio 集成 |
| `codex-ollama` | Ollama 集成 |

## 核心接口/API

### `get_default_model_for_oss_provider(provider_id: &str) -> Option<&'static str>`

根据提供商 ID 返回其默认模型名称。

```rust
let model = get_default_model_for_oss_provider("lmstudio");
// 返回 LM Studio 的默认模型名
```

### `ensure_oss_provider_ready(provider_id: &str, config: &Config) -> Result<(), std::io::Error>`

异步函数，确保指定的 OSS 提供商已就绪。对于 Ollama 还会额外检查是否支持 responses API。未知的提供商 ID 会静默跳过。
