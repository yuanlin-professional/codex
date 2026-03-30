# `config.rs` — Rollout 配置类型和视图 trait

## 功能概述
该文件定义了 `RolloutConfig` 结构体和 `RolloutConfigView` trait，提供 rollout 子系统所需的配置视图。包含 codex_home、sqlite_home、cwd、model_provider_id 和 generate_memories 五个配置项。为 `&T`、`Arc<T>` 实现了自动 trait 委托。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RolloutConfig` | 结构体 | Rollout 配置：codex_home, sqlite_home, cwd, provider, memories |
| `RolloutConfigView` | trait | 只读配置视图接口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RolloutConfig::from_view` | `(view) -> Self` | 从视图 trait 创建具体配置 |

## 依赖关系
- **引用的 crate**: 无
- **被引用方**: `recorder.rs`, `state_db.rs`, `metadata.rs`

## 实现备注
- `Config` 是 `RolloutConfig` 的类型别名
