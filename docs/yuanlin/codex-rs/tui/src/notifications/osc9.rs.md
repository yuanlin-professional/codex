# `osc9.rs` — OSC 9 桌面通知后端

通过输出 OSC 9 转义序列（`\x1b]9;{message}\x07`）触发终端桌面通知。`Osc9Backend` 实现了 `notify` 方法，将消息内容嵌入 OSC 9 序列中。支持 WezTerm、iTerm、Ghostty、Kitty 等终端。
