# `footer.rs` -- 底部面板页脚渲染

## 功能概述
该文件负责底部面板页脚区域的渲染逻辑，包括快捷键提示、退出确认提示、协作模式指示器、上下文窗口使用量显示和状态行等。实现了基于宽度的自适应折叠逻辑，当终端窗口变窄时按优先级逐步隐藏页脚元素，确保最重要的信息始终可见。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `FooterProps` | 结构体 | 页脚渲染输入参数，包含模式、标志、上下文信息等 |
| `FooterMode` | 枚举 | 页脚模式：QuitShortcutReminder、ShortcutOverlay、EscHint、ComposerEmpty、ComposerHasDraft |
| `CollaborationModeIndicator` | 枚举 | 协作模式指示器：Plan、PairProgramming、Execute |
| `SummaryLeft` | 枚举 | 单行页脚左侧布局选择：Default、Custom(Line)、None |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `single_line_footer_layout` | `fn single_line_footer_layout(...) -> (SummaryLeft, bool)` | 计算单行页脚布局和右侧上下文是否可见 |
| `render_footer_from_props` | `fn render_footer_from_props(area, buf, props, ...)` | 从 FooterProps 直接渲染页脚 |
| `render_footer_line` | `fn render_footer_line(area, buf, line)` | 渲染单个预计算的页脚行 |
| `passive_footer_status_line` | `fn passive_footer_status_line(props) -> Option<Line>` | 获取被动上下文页脚行（状态行/线程标签） |
| `context_window_line` | `fn context_window_line(percent, used_tokens) -> Line` | 生成上下文窗口使用量显示行 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`
- **被引用方**: `ChatComposer` 使用页脚渲染函数

## 实现备注
- 宽度折叠优先级：完整提示 > 队列提示 > 快捷键提示 > 模式标签 > 右侧上下文
- 快捷键面板（ShortcutOverlay）采用两列布局，包含 11 个快捷键描述
- WSL 环境下粘贴图片快捷键显示 Ctrl+Alt+V 而非 Ctrl+V
- 状态行启用时替代快捷键提示，支持与协作模式指示器共存
