# `url.rs` — Ollama URL 工具函数

提供 Ollama 相关的 URL 工具函数。`base_url_to_host_root` 将 provider base_url（如 `http://localhost:11434/v1`）转换为原生 Ollama 主机根路径（`http://localhost:11434`）。`is_openai_compatible_base_url` 检测 URL 是否以 `/v1` 结尾表示 OpenAI 兼容模式。
