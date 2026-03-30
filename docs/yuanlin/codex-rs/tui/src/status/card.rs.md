# `card.rs` — /status 命令输出卡片

## 功能概述

本模块生成 `/status` 命令的完整输出卡片，包含模型信息、推理努力、沙箱策略、审批模式、Token 使用量、速率限制、会话 ID、工作目录、CLI 版本等字段。使用 `FieldFormatter` 将所有字段对齐渲染为带边框的历史记录单元格。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new_status_output_with_rate_limits` | `(...) -> Box<dyn HistoryCell>` | 生成包含速率限制的完整状态卡片 |
| `new_status_output` | `(...) -> Box<dyn HistoryCell>` | 生成基本状态卡片（测试用） |

## 依赖关系

- **引用的 crate**: `codex_core`, `codex_protocol`, `chrono`, `ratatui`
- **被引用方**: `ChatWidget` 处理 `/status` 命令

## 实现备注

- 使用 `FieldFormatter` 统一标签宽度对齐
- 状态卡片被包装在 `CompositeHistoryCell` 中以支持边框渲染
