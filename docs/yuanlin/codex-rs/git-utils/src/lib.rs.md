# `lib.rs` — git-utils crate 入口

## 功能概述
聚合并导出 git-utils crate 的所有公共 API，包括 `apply_git_patch`（补丁应用）、`merge_base_with_head`（分支 merge-base）、`GhostCommit` 相关类型（幽灵提交快照/恢复）和 `GitInfo`/`GitSha` 等信息收集类型。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `GitSha` | 结构体 | Git SHA 的透明包装类型 |
| `GhostCommit` | 结构体 | 幽灵提交详情：id, parent, 预存未跟踪文件/目录 |

## 依赖关系
- **引用的 crate**: `schemars`, `serde`, `ts_rs`
- **被引用方**: `codex-core`, `codex-chatgpt`, `cloud-tasks`
