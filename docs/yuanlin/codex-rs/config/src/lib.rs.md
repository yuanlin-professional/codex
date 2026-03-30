# `lib.rs` — codex-config crate 入口与公共 API 导出

## 功能概述

`codex-config` crate 的库入口文件，组织所有子模块并通过 `pub use` 重导出公共 API。定义了配置 TOML 文件名常量 `CONFIG_TOML_FILE`，并集中导出配置需求（requirements）、约束系统（constraint）、诊断（diagnostics）、指纹（fingerprint）、合并（merge）、覆盖（overrides）、项目根标记（project root markers）、执行策略（exec policy）、技能配置（skills）和配置层状态（state）等模块的公共类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 全部为重导出 | — | 从 `config_requirements`、`constraint`、`diagnostics`、`fingerprint`、`merge`、`overrides`、`project_root_markers`、`requirements_exec_policy`、`skills_config`、`state` 模块重导出 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `CONFIG_TOML_FILE` | 常量 `&str = "config.toml"` | 配置文件名常量 |

## 依赖关系

- **引用的 crate**: 间接通过各子模块引用
- **被引用方**: `codex-core`、`codex-tui`、`codex-cli` 等上层 crate

## 实现备注

- 此文件本身不含业务逻辑，仅作为统一的公共 API 表面。所有类型和函数均定义在各自的子模块中。
