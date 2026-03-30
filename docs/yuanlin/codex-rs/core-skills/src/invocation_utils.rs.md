# `invocation_utils.rs` — 技能隐式调用检测工具

## 功能概述
该文件提供技能隐式调用检测功能，用于在用户执行命令时自动识别是否触发了某个技能。支持两种检测方式：脚本运行检测（如 `python3 scripts/fetch.py`）和文档读取检测（如 `cat SKILL.md`）。同时提供构建隐式技能路径索引的功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立公开结构体 | — | 主要提供函数接口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_implicit_skill_path_indexes` | `(skills) -> (HashMap, HashMap)` | 构建按 scripts 目录和文档路径索引的隐式技能映射 |
| `detect_implicit_skill_invocation_for_command` | `(outcome, command, workdir) -> Option<SkillMetadata>` | 检测命令是否隐式调用了某个技能 |
| `script_run_token` | `(tokens) -> Option<&str>` | 从命令 token 中识别脚本运行路径 |
| `detect_skill_script_run` | `(outcome, tokens, workdir) -> Option<SkillMetadata>` | 通过脚本路径匹配技能 |
| `detect_skill_doc_read` | `(outcome, tokens, workdir) -> Option<SkillMetadata>` | 通过文档路径匹配技能 |

## 依赖关系
- **引用的 crate**: `shlex`
- **被引用方**: `codex-core` 工具执行路径

## 实现备注
- 支持 10 种脚本运行器和 7 种脚本扩展名
- 支持 8 种文件读取命令的检测
- 路径匹配使用 `fs::canonicalize` 进行规范化
