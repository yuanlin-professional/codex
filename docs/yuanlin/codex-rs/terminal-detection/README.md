# terminal-detection

## 功能概述

`codex-terminal-detection` 是 Codex 项目的终端类型检测模块，通过分析环境变量来识别用户正在使用的终端模拟器。检测结果用于 OpenTelemetry User-Agent 日志标记以及 TUI 的终端特定配置选择。支持识别十余种主流终端以及 tmux/zellij 终端复用器。

## 架构说明

检测机制采用多级探测策略，按优先级依次检查：

1. **TERM_PROGRAM 环境变量** - 最直接的终端标识。若值为 `tmux`，则进一步通过 `tmux display-message` 查询底层真实终端。
2. **终端专属环境变量** - 如 `WEZTERM_VERSION`、`ITERM_SESSION_ID`、`KITTY_WINDOW_ID`、`WT_SESSION` 等。
3. **TERM 环境变量** - 能力字符串回退（如 `xterm-256color`、`dumb`）。

### 支持的终端

| 终端名称 | 检测方式 |
|---------|---------|
| Apple Terminal | `TERM_PROGRAM` / `TERM_SESSION_ID` |
| Ghostty | `TERM_PROGRAM` |
| iTerm2 | `TERM_PROGRAM` / `ITERM_SESSION_ID` |
| Warp | `TERM_PROGRAM` |
| VS Code | `TERM_PROGRAM` |
| WezTerm | `TERM_PROGRAM` / `WEZTERM_VERSION` |
| kitty | `KITTY_WINDOW_ID` / `TERM` 含 "kitty" |
| Alacritty | `ALACRITTY_SOCKET` / `TERM=alacritty` |
| Konsole | `KONSOLE_VERSION` |
| GNOME Terminal | `GNOME_TERMINAL_SCREEN` |
| VTE | `VTE_VERSION` |
| Windows Terminal | `WT_SESSION` |

### 复用器检测

- **tmux** - 通过 `TMUX` / `TMUX_PANE` 环境变量检测，并使用 `tmux display-message` 获取底层终端类型。
- **zellij** - 通过 `ZELLIJ` / `ZELLIJ_SESSION_NAME` / `ZELLIJ_VERSION` 环境变量检测。

### 结果缓存

检测结果通过 `OnceLock<TerminalInfo>` 全局缓存，保证只检测一次。

## 目录结构

```
terminal-detection/
  Cargo.toml
  src/
    lib.rs              # 完整的检测逻辑实现
    terminal_tests.rs   # 单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `tracing` | 环境变量读取异常日志 |

该模块的依赖极为精简，仅依赖 `tracing` 用于日志，不引入外部运行时或系统库。

## 核心接口/API

### 公共函数

- **`user_agent() -> String`** - 返回经过清理的终端标识符，适合用于 HTTP User-Agent 头。
- **`terminal_info() -> TerminalInfo`** - 返回结构化的终端元数据（带全局缓存）。

### 类型定义

```rust
pub struct TerminalInfo {
    pub name: TerminalName,                  // 检测到的终端类型
    pub term_program: Option<String>,        // TERM_PROGRAM 值
    pub version: Option<String>,             // 终端版本
    pub term: Option<String>,                // TERM 值
    pub multiplexer: Option<Multiplexer>,    // 复用器信息
}

pub enum TerminalName {
    AppleTerminal, Ghostty, Iterm2, WarpTerminal, VsCode, WezTerm,
    Kitty, Alacritty, Konsole, GnomeTerminal, Vte, WindowsTerminal,
    Dumb, Unknown,
}

pub enum Multiplexer {
    Tmux { version: Option<String> },
    Zellij {},
}
```
