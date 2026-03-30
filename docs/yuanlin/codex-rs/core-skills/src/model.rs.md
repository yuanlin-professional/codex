# `model.rs` — 技能数据模型定义

## 功能概述
该文件定义了 Codex 技能系统的核心数据模型，包括技能元数据、策略、接口、依赖项和加载结果。`SkillLoadOutcome` 是技能加载的完整结果，包含已加载技能、错误、禁用路径和隐式调用索引。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillMetadata` | 结构体 | 技能元数据：名称、描述、接口、依赖、策略、路径和作用域 |
| `SkillPolicy` | 结构体 | 技能策略：是否允许隐式调用和产品限制 |
| `SkillInterface` | 结构体 | 技能界面元数据：显示名、图标、品牌色、默认提示等 |
| `SkillDependencies` | 结构体 | 技能依赖项集合 |
| `SkillToolDependency` | 结构体 | 单个工具依赖项（类型、值、描述、传输方式等） |
| `SkillError` | 结构体 | 技能加载错误（路径 + 消息） |
| `SkillLoadOutcome` | 结构体 | 技能加载结果：技能列表、错误、禁用路径和隐式调用索引 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_skill_enabled` | `(&self, skill) -> bool` | 检查技能是否启用 |
| `allowed_skills_for_implicit_invocation` | `(&self) -> Vec<SkillMetadata>` | 获取所有允许隐式调用的技能 |
| `filter_skill_load_outcome_for_product` | `(outcome, product) -> SkillLoadOutcome` | 按产品过滤技能加载结果 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **被引用方**: 整个 `core-skills` crate 和 `codex-core`

## 实现备注
- `SkillLoadOutcome` 中的隐式调用索引使用 `Arc<HashMap>` 实现共享
