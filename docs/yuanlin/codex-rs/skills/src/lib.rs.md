# `lib.rs` — 系统技能安装管理

## 功能概述

管理嵌入式系统技能的磁盘安装。使用 `include_dir!` 宏在编译时嵌入 `src/assets/samples` 目录的内容，运行时将其解压到 `CODEX_HOME/skills/.system`。通过指纹标记文件避免重复安装：计算嵌入目录所有文件的哈希指纹，与标记文件对比，仅在不匹配时重新安装。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SystemSkillsError` | enum | 系统技能安装错误（IO 错误 + 操作描述） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `install_system_skills` | `(codex_home) -> Result<(), SystemSkillsError>` | 安装嵌入式系统技能到磁盘 |
| `system_cache_root_dir` | `(codex_home) -> PathBuf` | 返回系统技能缓存根目录路径 |
| `embedded_system_skills_fingerprint` | `() -> String` | 计算嵌入目录的哈希指纹 |
| `write_embedded_dir` | `(dir, dest) -> Result<()>` | 递归写入嵌入目录到磁盘 |

## 依赖关系

- **引用的 crate**: `include_dir`、`codex_utils_absolute_path`、`thiserror`
- **被引用方**: 应用启动时的技能初始化

## 实现备注

- 指纹使用 DefaultHasher 计算所有文件路径和内容的哈希，带版本盐值 "v1"。
- 安装时先删除已有目录再完整写入，保证原子性。
