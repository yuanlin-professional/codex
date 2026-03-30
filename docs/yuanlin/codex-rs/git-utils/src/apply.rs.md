# `apply.rs` — Git 补丁应用引擎

## 功能概述
封装 `git apply --3way` 的调用逻辑，将统一 diff 写入临时文件后执行，并解析命令输出为结构化的 applied/skipped/conflicted 路径分组。支持 preflight 模式（`--check` 干跑）和 revert 模式。包含从 TypeScript 移植的完整 `git apply` 输出解析器。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApplyGitRequest` | 结构体 | 请求参数：cwd, diff, revert, preflight |
| `ApplyGitResult` | 结构体 | 结果：exit_code, applied/skipped/conflicted paths, stdout/stderr |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_git_patch` | `fn(req) -> io::Result<ApplyGitResult>` | 应用 Git 补丁主入口 |
| `extract_paths_from_patch` | `fn(diff_text) -> Vec<String>` | 从 diff 头提取所有涉及路径 |
| `parse_git_apply_output` | `fn(stdout, stderr) -> (applied, skipped, conflicted)` | 解析 git apply 输出 |
| `stage_paths` | `fn(git_root, diff) -> io::Result<()>` | 暂存 diff 涉及的已存在文件 |

## 依赖关系
- **引用的 crate**: `regex`, `once_cell`, `tempfile`
- **被引用方**: `codex-chatgpt` apply_command, cloud-tasks apply

## 实现备注
包含引号路径处理、C 风格转义反序列化、以及 preflight 不修改索引的测试验证。
