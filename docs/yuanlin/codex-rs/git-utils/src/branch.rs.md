# `branch.rs` — Git 分支 merge-base 计算

## 功能概述
计算当前 HEAD 与指定分支之间的 merge-base commit。当远程分支比本地分支更新时，优先使用远程引用进行计算。处理 HEAD 不存在或分支不可解析的边界情况，返回 `Ok(None)` 而非错误。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `merge_base_with_head` | `fn(repo_path, branch) -> Result<Option<String>>` | 计算 HEAD 与指定分支的 merge-base |

## 依赖关系
- **引用的 crate**: `crate::operations`
- **被引用方**: cloud-tasks diff 计算
