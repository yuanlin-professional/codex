# `lib.rs` — rustls 加密提供者初始化

确保进程范围内安装一个 rustls 加密提供者。当依赖图中同时启用 `ring` 和 `aws-lc-rs` 特性时，rustls 无法自动选择提供者，本函数使用 `Once` 确保只初始化一次 `ring` 提供者。
