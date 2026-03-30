# `vt100_live_commit.rs` — 测试模块

## 测试覆盖范围

使用 VT100 虚拟终端后端测试 TUI 的"实时提交"机制：当活跃的行缓冲区（live ring）溢出时，超出部分的行会被提交到终端的历史区域。需启用 `vt100-tests` feature 才会编译运行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `live_001_commit_on_overflow` | 在 20x6 的虚拟终端中通过 `RowBuilder` 构建 5 行文本 ("one" 到 "five")，调用 `drain_commit_ready(3)` 保留最后 3 行在 live ring 中、提交前 2 行（"one"、"two"），然后通过 `insert_history_lines` 写入终端。验证屏幕上可见 "one" 和 "two" 已被提交到视口上方区域。 |
