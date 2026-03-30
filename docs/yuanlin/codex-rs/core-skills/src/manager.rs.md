# `manager.rs` — 技能管理器，负责技能加载、缓存与禁用过滤

## 功能概述
`SkillsManager` 是技能系统的核心管理器，负责按工作目录或配置加载技能、管理缓存以及应用禁用规则。它支持两种缓存策略：基于 cwd 的缓存和基于配置的缓存，确保会话级别技能覆盖不会跨会话泄漏。管理器在初始化时处理系统内置技能的安装或卸载。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillsLoadInput` | 结构体 | 技能加载输入参数，包含 cwd、技能根路径、配置层级栈和内置技能开关 |
| `SkillsManager` | 结构体 | 技能管理器，持有 codex_home、产品限制和两级缓存（cwd/config） |
| `ConfigSkillsCacheKey` | 结构体(内部) | 配置感知缓存的键，由技能根路径和配置规则组成 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `SkillsManager::new` | `(codex_home, bundled_skills_enabled) -> Self` | 创建管理器，默认 Codex 产品限制 |
| `skills_for_config` | `(&self, input) -> SkillLoadOutcome` | 基于配置加载技能，使用配置感知缓存 |
| `skills_for_cwd` | `async (&self, input, force_reload) -> SkillLoadOutcome` | 基于工作目录加载技能，支持强制重载 |
| `skills_for_cwd_with_extra_user_roots` | `async (&self, ...) -> SkillLoadOutcome` | 支持额外用户技能根路径的加载 |
| `bundled_skills_enabled_from_stack` | `(stack) -> bool` | 从配置栈中读取内置技能开关状态 |
| `clear_cache` | `(&self)` | 清空所有缓存 |

## 依赖关系
- **引用的 crate**: `codex_config`, `codex_protocol`
- **被引用方**: `codex-core` 中的会话管理器

## 实现备注
- 缓存使用 `RwLock<HashMap>` 实现，支持中毒恢复（`PoisonError::into_inner`）
- 额外用户根路径会经过规范化、排序和去重处理
