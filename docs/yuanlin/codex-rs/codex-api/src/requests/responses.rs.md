# `responses.rs` — 请求工具函数

定义 `Compression` 枚举（None/Zstd）和 `attach_item_ids` 函数。`attach_item_ids` 用于 Azure 端点兼容性，将 `ResponseItem` 中的 ID 注入到序列化后的 JSON input 数组中。
