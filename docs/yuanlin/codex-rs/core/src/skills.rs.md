# `skills.rs` — 技能系统集成与依赖管理

## 功能概述

此文件是 `codex_core_skills` crate 的集成层，负责将技能系统的各种类型和功能重新导出，并提供与 Codex 会话系统的桥接。主要包括三部分功能：从配置构建技能加载输入、为回合解析技能依赖（包括通过用户交互请求缺失的环境变量），以及检测和上报隐式技能调用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| （大量 re-export） | — | 从 `codex_core_skills` 重新导出：`SkillsManager`、`SkillMetadata`、`SkillLoadOutcome`、`SkillPolicy` 等 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `skills_load_input_from_config` | `(config, effective_skill_roots) -> SkillsLoadInput` | 从配置和有效技能根目录构建技能加载输入 |
| `resolve_skill_dependencies_for_turn` | `async (sess, turn_context, dependencies)` | 为回合解析技能依赖：先从环境变量获取，缺失的通过用户交互请求 |
| `request_skill_dependencies` | `async (sess, turn_context, dependencies)` | 通过 `request_user_input` 向用户请求缺失的环境变量值 |
| `maybe_emit_implicit_skill_invocation` | `async (sess, turn_context, command, workdir)` | 检测命令是否隐式触发了技能，并发送遥测事件（每回合每技能仅一次） |

## 依赖关系

- **引用的 crate**: `codex_core_skills`（核心技能库）、`codex_analytics`（遥测上报）、`codex_protocol`（用户输入请求）
- **被引用方**: `lib.rs` 广泛导出技能相关类型和函数供内部使用

## 实现备注

- 技能依赖解析会去重（通过 `seen_names` 集合），且跳过已在 `dependency_env` 中存在的变量
- 用户输入请求标记为 `is_secret: true`，值仅存储在内存中不会持久化
- 用户输入响应中通过 `"user_note: "` 前缀提取实际值
- 隐式技能调用通过 `{scope}:{path}:{name}` 键去重，确保同一技能在同一回合中只上报一次
- 遥测事件包括 OTel counter（`codex.skill.injected`）和分析事件（`track_skill_invocations`）
