# `mod.rs` — 运行时模块入口

## 功能概述
`runtimes` 模块的入口文件，定义了具体工具运行时（shell、apply_patch、unified_exec）的共享辅助函数。核心功能包括构建沙箱命令和用 shell 快照包装 `-lc` 命令（POSIX），后者通过在用户 shell 中加载快照文件来恢复环境变量上下文。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_sandbox_command` | `fn(command, cwd, env, additional_permissions) -> Result<SandboxCommand, ToolError>` | 从命令行数组构建 `SandboxCommand` |
| `maybe_wrap_shell_lc_with_snapshot` | `fn(command, session_shell, cwd, explicit_env_overrides) -> Vec<String>` | 用快照包装 `shell -lc` 命令，保留显式环境变量覆盖 |
| `build_override_exports` | `fn(explicit_env_overrides) -> (captures, exports)` | 生成环境变量捕获和恢复的 shell 脚本片段 |
| `shell_single_quote` | `fn(input) -> String` | POSIX shell 单引号转义 |

## 依赖关系
- **子模块**: `apply_patch`, `shell`, `unified_exec`
- **引用的 crate**: `codex_sandboxing`, `codex_protocol`
- **被引用方**: `shell.rs`, `unified_exec.rs` 中使用共享辅助函数

## 实现备注
- 快照包装仅在 POSIX 系统上生效，Windows 上为 no-op
- 环境变量覆盖机制：在加载快照前捕获显式覆盖值，加载后恢复，避免快照覆盖用户指定的变量
- 仅在命令 cwd 与快照 cwd 匹配时执行包装，使用路径规范化处理 `.` 别名
