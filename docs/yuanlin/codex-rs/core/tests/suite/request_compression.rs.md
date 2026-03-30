# `request_compression.rs` -- 测试模块

## 测试覆盖范围
测试请求体的 zstd 压缩功能。验证在启用 EnableRequestCompression feature 后，ChatGPT 后端认证时请求被 zstd 压缩，而 API key 认证时不压缩。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `request_body_is_zstd_compressed_for_codex_backend_when_enabled` | ChatGPT auth + EnableRequestCompression 时，请求 Content-Encoding 为 "zstd"，解压后为有效的 Responses API JSON |
| `request_body_is_not_compressed_for_api_key_auth_even_when_enabled` | API key auth + EnableRequestCompression 时，请求无 Content-Encoding header，body 为明文 JSON |
