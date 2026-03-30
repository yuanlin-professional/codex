# `vt100_history.rs` — 测试模块

## 测试覆盖范围

使用 VT100 虚拟终端后端对 TUI 的历史行插入（`insert_history_lines`）功能进行端到端测试，覆盖基本插入、长文本换行、Emoji/CJK 字符、ANSI 样式 span、光标恢复以及自动换行策略等场景。需启用 `vt100-tests` feature 才会编译运行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `basic_insertion_no_wrap` | 在 20x6 的虚拟终端中插入 "first" 和 "second" 两行，验证屏幕内容包含这两个字符串。 |
| `long_token_wraps` | 插入长度为 45 的 'A' 重复串（超过屏幕宽度 20 的两倍），验证屏幕上所有 'A' 字符数等于 45，确保换行时不丢字符。 |
| `emoji_and_cjk` | 插入包含 Emoji 和中文字符的文本 "😀😀😀😀😀 你好世界"，验证屏幕内容包含所有非空白字符。 |
| `mixed_ansi_spans` | 插入包含红色 ANSI span 和普通文本的行 `"red" + "+plain"`，验证屏幕显示 "red+plain"。 |
| `cursor_restoration` | 插入一行 "x" 后，验证终端光标位置恢复到 (0, 0)。 |
| `word_wrap_no_mid_word_split` | 在 40x10 的终端中插入长段英文文本，验证 "both" 一词不会被拆分到两行（即不出现 "bo\nth"）。 |
| `em_dash_and_space_word_wrap` | 在 40x10 的终端中插入含 em-dash 的英文文本，验证 "inside" 一词不会被拆分到两行（即不出现 "insi\nde"）。 |
