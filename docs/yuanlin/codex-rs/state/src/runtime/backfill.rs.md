# `backfill.rs` — Rollout 元数据回填 SQLite 操作

## 功能概述
该文件为 `StateRuntime` 实现 rollout 元数据回填的生命周期管理。包括回填状态的读取、认领（基于租约的单例控制）、进度检查点、完成标记等操作。确保同一时间只有一个工作者执行回填。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无新增类型 | — | 类型定义在 `model/backfill_state.rs` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_backfill_state` | `async (&self) -> Result<BackfillState>` | 获取当前回填状态 |
| `try_claim_backfill` | `async (&self, lease_seconds) -> Result<bool>` | 尝试认领回填工作者（租约过期后可重认领） |
| `checkpoint_backfill` | `async (&self, watermark) -> Result<()>` | 持久化回填进度 |
| `mark_backfill_complete` | `async (&self, last_watermark) -> Result<()>` | 标记回填完成 |

## 依赖关系
- **引用的 crate**: `sqlx`, `chrono`
- **被引用方**: `codex-rollout` 回填流程

## 实现备注
- 使用 `updated_at` + `lease_seconds` 实现租约过期检测
- 完成状态的回填不能被重新认领
