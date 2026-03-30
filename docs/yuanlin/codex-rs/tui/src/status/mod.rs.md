# `mod.rs` — 状态输出模块入口

## 功能概述

状态输出格式化和显示适配器的模块入口。将协议级快照转换为 `/status` 输出和 footer/status-line 使用的稳定显示结构。组织 account（账户）、card（卡片）、format（格式化）、helpers（辅助）、rate_limits（速率限制）五个子模块，并重新导出关键类型和函数。

## 依赖关系

- **被引用方**: `ChatWidget`、`App` 状态显示相关功能
