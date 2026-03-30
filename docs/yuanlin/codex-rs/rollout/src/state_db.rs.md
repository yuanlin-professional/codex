# `state_db.rs` — StateDb 初始化与辅助操作

## 功能概述
该文件提供 Rollout 子系统对 SQLite 状态数据库的初始化和辅助操作。`init` 函数初始化 `StateRuntime` 并触发回填检查。`StateDbHandle` 是 `Arc<codex_state::StateRuntime>` 的类型别名。还包含 cwd 路径规范化等工具函数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `StateDbHandle` | 类型别名 | `Arc<codex_state::StateRuntime>` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `init` | `async (config) -> Option<StateDbHandle>` | 初始化状态运行时并检查回填状态 |

## 依赖关系
- **引用的 crate**: `codex_state`, `codex_utils_path`
- **被引用方**: `recorder.rs`, `codex-core`

## 实现备注
- 初始化失败时返回 None 而非 panic
