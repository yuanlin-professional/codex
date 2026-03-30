# `apply_command.rs` — 应用云端任务 diff 命令

## 功能概述
实现 `apply` 子命令，从 Codex 云端获取指定任务的最新 diff 输出，然后在本地仓库中通过 `git apply --3way` 应用补丁。支持 CLI 参数解析和自定义工作目录。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyCommand` | 结构体 | CLI 参数定义，包含 task_id 和配置覆盖项 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_apply_command` | `async fn(cli, cwd) -> Result<()>` | 完整命令执行流程：加载配置、认证、获取任务、应用 diff |
| `apply_diff_from_task` | `async fn(response, cwd) -> Result<()>` | 从任务响应中提取 diff 并应用 |

## 依赖关系
- **引用的 crate**: `codex_core`, `codex_git_utils`, `clap`
- **被引用方**: CLI 主入口、集成测试
