# `lib.rs` — LM Studio 模块入口

`lmstudio` crate 的入口文件，导出 `LMStudioClient` 和 `ensure_oss_ready` 函数。定义默认 OSS 模型为 `openai/gpt-oss-20b`。`ensure_oss_ready` 验证 LM Studio 服务器可达性，检查模型是否本地可用并在需要时下载，然后在后台加载模型。
