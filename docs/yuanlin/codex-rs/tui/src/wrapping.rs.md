# `wrapping.rs` — URL 感知的自适应文本换行

## 功能概述

为 TUI 提供文本换行功能，核心特点是 URL 感知：当文本中包含 URL 时，自动切换到
URL 保护模式，确保 URL 不被拆分（保持终端中的可点击性）。提供标准换行和自适应换行
两种路径，以及字节范围映射（用于光标定位）和多种输入类型适配。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RtOptions` | struct | 包装 textwrap Options 并扩展 ratatui Line 缩进 |
| `LineInput` | enum | 借用或拥有的 Line 输入 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `word_wrap_line` | `(line, opts) -> Vec<Line>` | 单行标准换行 |
| `word_wrap_lines` | `(lines, opts) -> Vec<Line>` | 多行标准换行 |
| `adaptive_wrap_line` | `(line, opts) -> Vec<Line>` | 单行自适应换行（URL 检测） |
| `adaptive_wrap_lines` | `(lines, opts) -> Vec<Line>` | 多行自适应换行 |
| `wrap_ranges` | `(text, opts) -> Vec<Range>` | 返回换行后的源文本字节范围（含尾部空白+哨兵） |
| `wrap_ranges_trim` | `(text, opts) -> Vec<Range>` | 返回换行后的源文本字节范围（不含尾部） |
| `text_contains_url_like` | `(text) -> bool` | 检测文本中是否包含 URL |
| `url_preserving_wrap_options` | `(opts) -> RtOptions` | 生成 URL 保护换行选项 |

## 依赖关系

- **引用的 crate**: `textwrap`, `ratatui`
- **引用的模块**: `crate::render::line_utils`

## 实现备注

- URL 检测是启发式的：识别 `scheme://host`、裸域名+路径/查询/片段、`localhost:port`、IPv4 等。
- 自适应模式下，URL token 不产生分割点，非 URL token 在每个字符边界都可分割。
- `wrap_ranges` 通过 `map_owned_wrapped_line_to_range` 处理 textwrap 产生的 `Cow::Owned`
  输出（含连字符惩罚字符），将其映射回源文本字节位置。
- `IntoLineInput` trait 允许 `&Line`、`Line`、`String`、`&str`、`Span`、`Vec<Span>` 等多种输入类型。
- `RtOptions` 的 initial/subsequent indent 使用 ratatui `Line` 而非纯字符串，
  支持带样式的缩进前缀。
