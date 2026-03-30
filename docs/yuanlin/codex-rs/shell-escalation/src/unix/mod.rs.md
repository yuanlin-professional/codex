# `mod.rs` — Unix shell-escalation 协议实现

## 功能概述
Unix 下 shell 权限提升协议的根模块。包含详细的 ASCII 流程图说明 Escalation 和 Non-Escalation 两种工作流。聚合并导出所有子模块（client/server/protocol/policy/socket/wrapper/stopwatch）的公共类型。

## 依赖关系
- **被引用方**: `lib.rs`（条件编译导出）
