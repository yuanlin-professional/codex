# `parse_command.rs` — 命令解析结果类型

`ParsedCommand` 枚举定义了对 Agent 发出的 shell 命令的语义解析结果。四种变体：`Read`（文件读取，含路径）、`ListFiles`（列出文件）、`Search`（搜索查询）和 `Unknown`（无法识别的命令）。用于在审批 UI 中向用户展示命令的意图分类，辅助 TUI 渲染和审批决策。
