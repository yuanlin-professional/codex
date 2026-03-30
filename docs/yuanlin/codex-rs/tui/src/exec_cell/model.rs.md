# `model.rs` — 执行单元数据模型

## 功能概述

定义 TUI 聊天记录中命令执行历史单元的数据模型。`ExecCell` 可以表示单个命令或一组相关的"探索"命令（读取、列表、搜索）。ChatWidget 依赖 `call_id` 匹配来路由进度和结束事件到正确的单元格，"未找到 call_id" 被视为真实信号（例如孤立的 end 事件应渲染为独立历史条目）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecCell` | struct | 命令执行单元格，包含一组调用和动画配置 |
| `ExecCall` | struct | 单次命令调用，包含 call_id、命令、解析结果、输出、来源、计时等 |
| `CommandOutput` | struct | 命令输出，包含 exit_code、聚合输出和格式化输出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_added_call` | `(&self, ...) -> Option<Self>` | 尝试向探索型单元格添加新调用 |
| `complete_call` | `(&mut self, call_id, output, duration) -> bool` | 标记匹配的调用为完成，返回是否找到 |
| `is_exploring_cell` | `(&self) -> bool` | 判断是否为探索型单元格（仅包含读取/列表/搜索） |
| `append_output` | `(&mut self, call_id, chunk) -> bool` | 追加流式输出到匹配的调用 |

## 依赖关系

- **引用的 crate**: `codex_protocol::parse_command`, `codex_protocol::protocol`
- **被引用方**: `exec_cell::render`, `ChatWidget` 命令处理

## 实现备注

- 探索型单元格仅包含 Read/ListFiles/Search 类型的解析命令
- `complete_call` 从后向前查找，优先匹配最新的同名调用
- UserShell 来源的命令不参与探索型分组
