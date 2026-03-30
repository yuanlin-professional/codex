# `slash_commands.rs` -- 斜杠命令过滤和匹配辅助

## 功能概述
该文件提供了内置斜杠命令的过滤和匹配辅助函数，集中管理沙盒和功能门控规则，确保编辑器和命令弹出窗口使用一致的可见性逻辑。支持按功能标志过滤可用命令、精确名称查找和模糊前缀匹配。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BuiltinCommandFlags` | 结构体 | 控制命令可见性的功能标志集合 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `builtins_for_input` | `fn builtins_for_input(flags) -> Vec<(&str, SlashCommand)>` | 返回当前配置下可见的内置命令列表 |
| `find_builtin_command` | `fn find_builtin_command(name, flags) -> Option<SlashCommand>` | 精确查找单个命令 |
| `has_builtin_prefix` | `fn has_builtin_prefix(name, flags) -> bool` | 检查是否有匹配前缀的命令 |

## 依赖关系
- **引用的 crate**: `codex_utils_fuzzy_match`
- **被引用方**: `ChatComposer` 和 `CommandPopup`

## 实现备注
- 八个独立的功能门控标志控制不同命令的可见性
- `elevate-sandbox` 仅在 Windows 降级沙盒活跃时可用
- `find_builtin_command` 先解析名称再检查可见性，支持别名（如 clean -> stop）
