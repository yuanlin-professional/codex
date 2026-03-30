# `highlight.rs` — 语法高亮引擎

## 功能概述

本模块基于 syntect 和 two_face 提供约 250 种语言的语法高亮和 32 种内置配色主题。它管理四个进程全局单例（语法集、当前主题、主题覆盖、codex 主目录），支持运行时主题切换（实时预览）、自定义 `.tmTheme` 文件加载，以及 ANSI 调色板语义的正确处理。输入超过 512KB 或 10000 行时会跳过高亮以防止性能问题。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DiffScopeBackgroundRgbs` | struct | 从主题中提取的 diff 背景色（inserted/deleted） |
| `ThemeEntry` | struct | 主题选择器中的条目（名称 + 是否自定义） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_theme_override` | `(name, codex_home) -> Option<String>` | 设置用户配置的主题覆盖（启动时调用一次） |
| `highlight_code_to_lines` | `(code, lang) -> Vec<Line>` | 高亮代码并返回 ratatui Line（含回退路径） |
| `highlight_bash_to_lines` | `(script) -> Vec<Line>` | bash 脚本高亮的便捷封装 |
| `set_syntax_theme` | `(theme)` | 运行时切换主题（实时预览） |
| `list_available_themes` | `(codex_home) -> Vec<ThemeEntry>` | 列出所有可用主题（内置 + 自定义） |
| `diff_scope_background_rgbs` | `() -> DiffScopeBackgroundRgbs` | 查询当前主题的 diff 背景色 |

## 依赖关系

- **引用的 crate**: `syntect`, `two_face`, `ratatui`
- **被引用方**: `markdown_render`、`exec_cell`、`diff_render`

## 实现备注

- ANSI 系列主题（ansi/base16/base16-256）通过 alpha 通道编码 ANSI 调色板语义
- 自适应默认主题：亮色背景选 catppuccin-latte，暗色选 catppuccin-mocha
- 自定义主题放在 `{codex_home}/themes/{name}.tmTheme`
- 故意跳过 italic 和 underline 修饰（终端渲染效果差）
- 包含 30+ 个全面的单元测试
