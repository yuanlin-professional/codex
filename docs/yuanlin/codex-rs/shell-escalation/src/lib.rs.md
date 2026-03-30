# `lib.rs` — shell-escalation crate 入口

## 功能概述
条件编译 Unix 模块，并将所有平台相关的权限提升协议类型（`EscalateAction`, `EscalateServer`, `EscalationPolicy`, `ExecParams`, `ExecResult` 等）导出为 crate 公共 API。在非 Unix 平台不导出任何符号。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `EscalateAction` | 枚举 | 提升决策动作：Run/Escalate/Deny |
| `EscalateServer` | 结构体 | 提升服务端 |
| `EscalationSession` | 结构体 | 提升会话 |
| `ExecParams` | 结构体 | 命令执行参数 |
| `ExecResult` | 结构体 | 命令执行结果 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **被引用方**: `codex-core` 中的沙箱执行器
