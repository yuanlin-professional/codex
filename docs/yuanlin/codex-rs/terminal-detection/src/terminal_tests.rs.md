# `terminal_tests.rs` — 测试模块

## 测试覆盖范围

全面测试终端检测逻辑，通过 FakeEnvironment 注入模拟环境变量验证各终端类型和多路复用器的识别。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detects_term_program` | TERM_PROGRAM 检测（含版本、无版本、覆盖 WEZTERM） |
| `detects_iterm2` | 通过 ITERM_SESSION_ID 检测 iTerm2 |
| `detects_apple_terminal` | 通过 TERM_PROGRAM 和 TERM_SESSION_ID 检测 Apple Terminal |
| `detects_ghostty` | 通过 TERM_PROGRAM 检测 Ghostty |
| `detects_vscode` | 通过 TERM_PROGRAM + VERSION 检测 VS Code |
| `detects_warp_terminal` | 通过 TERM_PROGRAM 检测 Warp Terminal |
| `detects_tmux_multiplexer` | tmux 多路复用器检测和客户端 termtype/termname |
| `detects_zellij_multiplexer` | zellij 多路复用器检测 |
| `detects_tmux_client_termtype` | tmux 下检测底层终端类型 |
| `detects_tmux_client_termname` | tmux 下检测底层终端 termname |
| `detects_tmux_term_program_uses_client_termtype` | tmux TERM_PROGRAM 使用客户端 termtype 覆盖 |
| `detects_wezterm` | 通过 WEZTERM_VERSION 和 TERM_PROGRAM 检测 WezTerm |
| `detects_kitty` | 通过 KITTY_WINDOW_ID、TERM_PROGRAM、TERM 检测 kitty |
| `detects_alacritty` | 通过 ALACRITTY_SOCKET、TERM_PROGRAM、TERM 检测 Alacritty |
| `detects_konsole` | 通过 KONSOLE_VERSION 和 TERM_PROGRAM 检测 Konsole |
| `detects_gnome_terminal` | 通过 GNOME_TERMINAL_SCREEN 和 TERM_PROGRAM 检测 GNOME Terminal |
| `detects_vte` | 通过 VTE_VERSION 和 TERM_PROGRAM 检测 VTE |
| `detects_windows_terminal` | 通过 WT_SESSION 和 TERM_PROGRAM 检测 Windows Terminal |
| `detects_term_fallbacks` | TERM 回退、dumb 终端、未知终端 |
