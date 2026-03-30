# templates/review/ -- 会话回顾模板

## 功能概述

`review/` 包含 Codex 会话回顾（Review）功能使用的模板。回顾模板以 XML 和 Markdown 格式定义了会话结束时（成功完成或中断退出）如何构建回顾上下文和历史消息，供代码审查任务使用。

## 目录结构

```
review/
├── exit_success.xml                   # 成功退出时的回顾结果 XML 模板
├── exit_interrupted.xml               # 中断退出时的回顾结果 XML 模板
├── history_message_completed.md       # 完成时的历史消息模板
└── history_message_interrupted.md     # 中断时的历史消息模板
```

## 依赖关系

- **被引用方**：`src/tasks/review.rs`（ReviewTask 使用回顾模板构建审查上下文）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `exit_success.xml` | 成功退出模板：`<user_action>` XML 结构，包含 `{{results}}` 占位符封装审查结果 |
| `exit_interrupted.xml` | 中断退出模板：同样结构但适用于中断场景 |
| `history_message_completed.md` | 完成后历史消息模板 |
| `history_message_interrupted.md` | 中断后历史消息模板 |
