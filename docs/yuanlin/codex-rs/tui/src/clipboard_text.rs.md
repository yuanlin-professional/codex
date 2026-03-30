# `clipboard_text.rs` — 剪贴板文本复制支持

## 功能概述

本文件为 TUI 的 `/copy` 命令提供跨平台文本复制到系统剪贴板的功能。它根据运行环境选择最合适的复制策略：普通桌面会话使用 `arboard` 原生剪贴板、SSH 会话使用 OSC 52 转义序列（让用户终端代理复制）、WSL 环境在原生方式失败时回退到 `powershell.exe`。

模块设计故意保持窄范围：只处理文本复制，返回用户友好的错误字符串，不试图暴露可复用的剪贴板抽象。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `copy_text_to_clipboard` | `fn(text: &str) -> Result<(), String>` | 将文本复制到最合适的剪贴板 |
| `copy_via_osc52` | `fn(text) -> Result<(), String>` | 通过 OSC 52 转义序列复制（SSH 场景） |
| `copy_via_wsl_clipboard` | `fn(text) -> Result<(), String>` | 通过 WSL powershell.exe 复制（仅 Linux） |
| `osc52_sequence` | `fn(text, tmux) -> String` | 生成 OSC 52 剪贴板序列（支持 tmux 透传） |

## 依赖关系

- **引用的 crate**: `arboard`（原生剪贴板）、`base64`（OSC 52 编码）、`crate::clipboard_paste::is_probably_wsl`
- **被引用方**: TUI 的 `/copy` 斜杠命令处理

## 实现备注

- SSH 检测通过 `SSH_CONNECTION` 和 `SSH_TTY` 环境变量实现。
- OSC 52 在 Unix 上写入 `/dev/tty` 而非 stdout，确保即使 stdout 被重定向也能到达终端。
- tmux 环境下使用 `ESC Ptmux;` 透传包装。
- Android 平台返回"不支持"错误。
