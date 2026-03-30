# `loader.rs` — 技能文件发现、解析与加载

## 功能概述
该文件实现了 Codex 技能系统的完整加载流程：从多个技能根路径（Repo、User、System、Admin）中扫描 `SKILL.md` 文件，解析 YAML 前置元数据和可选的 `openai.yaml` 元数据文件，生成 `SkillMetadata`。支持符号链接跟随、深度限制、目录数量限制和字段长度校验。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SkillRoot` | 结构体 | 技能根路径及其作用域（Repo/User/System/Admin） |
| `SkillFrontmatter` | 结构体(内部) | SKILL.md 文件的 YAML 前置元数据 |
| `SkillMetadataFile` | 结构体(内部) | openai.yaml 元数据文件结构 |
| `SkillParseError` | 枚举(内部) | 技能解析错误类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_skills_from_roots` | `(roots: I) -> SkillLoadOutcome` | 从多个根路径加载技能并去重排序 |
| `skill_roots` | `(stack, cwd, plugin_roots) -> Vec<SkillRoot>` | 根据配置层级和当前目录计算所有技能根路径 |
| `discover_skills_under_root` | `(root, scope, outcome)` | BFS 扫描单个根目录下的所有技能文件 |
| `parse_skill_file` | `(path, scope) -> Result<SkillMetadata>` | 解析单个 SKILL.md 文件为技能元数据 |
| `extract_frontmatter` | `(contents) -> Option<String>` | 从 Markdown 中提取 `---` 分隔的 YAML 前置数据 |

## 依赖关系
- **引用的 crate**: `codex_config`, `codex_protocol`, `codex_utils_plugins`, `serde_yaml`, `dunce`
- **被引用方**: `manager.rs` 中的 `SkillsManager`

## 实现备注
- 扫描深度限制为 6 层，每个根最多 2000 个目录
- 非 System 作用域的技能支持符号链接跟随
- 插件技能名称自动添加命名空间前缀
