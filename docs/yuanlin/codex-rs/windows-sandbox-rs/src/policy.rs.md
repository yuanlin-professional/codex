# `policy.rs` — 沙盒策略解析与验证

## 功能概述
该文件提供沙盒策略字符串到 `SandboxPolicy` 枚举的解析功能。支持 `read-only` 和 `workspace-write` 预设值，以及 JSON 格式的自定义策略。`DangerFullAccess` 和 `ExternalSandbox` 策略被显式拒绝。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_policy` | `(value: &str) -> Result<SandboxPolicy>` | 将字符串解析为沙盒策略枚举 |

## 依赖关系
- **引用的 crate**: `anyhow`, `codex_protocol`, `serde_json`
- **被引用方**: `setup_orchestrator`, `elevated_impl`, `lib.rs`

## 实现备注
- 测试覆盖了预设值解析、JSON 解析和不安全策略拒绝
