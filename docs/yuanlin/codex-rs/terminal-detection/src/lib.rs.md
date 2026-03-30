# `lib.rs` — 终端检测工具

## 功能概述

通过环境变量检测当前终端模拟器的类型、版本和多路复用器信息。支持识别 Apple Terminal、Ghostty、iTerm2、Warp、VS Code、WezTerm、kitty、Alacritty、Konsole、GNOME Terminal、VTE、Windows Terminal 等终端。能透过 tmux/zellij 多路复用器检测底层终端（通过 `tmux display-message` 获取客户端信息）。提供 User-Agent 格式化输出用于 OpenTelemetry 日志。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TerminalInfo` | struct | 结构化终端信息：name、term_program、version、term、multiplexer |
| `TerminalName` | enum | 已知终端名称分类（14 种终端 + Unknown） |
| `Multiplexer` | enum | 终端多路复用器：Tmux（含版本）、Zellij |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `terminal_info` | `() -> TerminalInfo` | 获取缓存的终端信息（OnceLock） |
| `user_agent` | `() -> String` | 返回 User-Agent 格式的终端标识 |
| `detect_terminal_info_from_env` | `(env) -> TerminalInfo` | 核心检测逻辑，支持注入环境变量 |

## 依赖关系

- **引用的 crate**: `tracing`
- **被引用方**: OpenTelemetry 用户代理日志、TUI 配置

## 实现备注

- 检测优先级：TERM_PROGRAM > 终端特定变量 > TERM 回退。
- tmux 下通过 `tmux display-message -p` 获取底层终端 termtype/termname。
- User-Agent 值经过清洗，仅保留 ASCII 字母数字和 `-_./ ` 字符。
