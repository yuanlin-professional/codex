# `ui_consts.rs` — 共享 UI 布局常量

TUI 内部共享的布局常量。`LIVE_PREFIX_COLS`（2 列）定义实时单元格和对齐组件的
左侧边距宽度，被 chat composer、状态指示器和用户历史行的换行计算使用。
`FOOTER_INDENT_COLS` 为其 usize 类型别名。
