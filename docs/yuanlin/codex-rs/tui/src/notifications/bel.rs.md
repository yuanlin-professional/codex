# `bel.rs` — BEL 桌面通知后端

通过输出 ASCII BEL 字符（`\x07`）触发终端桌面通知。`BelBackend` 实现了 `notify` 方法，使用 crossterm 的 `Command` trait 写入 BEL 转义序列。在 Windows 上强制使用 ANSI 模式。
