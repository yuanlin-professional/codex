# `permissions.rs` — 文件系统沙箱策略引擎

## 功能概述

实现了 Codex 的文件系统沙箱策略引擎，定义了细粒度的文件系统访问控制类型和策略解析逻辑。支持三种策略模式（受限/不受限/外部沙箱）、基于路径的 read/write/none 访问控制、特殊路径标记（根目录、工作目录、项目根、临时目录等），以及与遗留 `SandboxPolicy` 类型之间的双向转换。核心能力包括路径访问权限查询、可写根目录计算、只读保护子路径推导，以及符号链接感知的路径规范化。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkSandboxPolicy` | 枚举 | 网络沙箱策略（Restricted / Enabled） |
| `FileSystemAccessMode` | 枚举 | 文件系统访问模式（Read / Write / None），按冲突优先级排序 |
| `FileSystemSpecialPath` | 枚举 | 特殊路径标识（Root / Minimal / CurrentWorkingDirectory / ProjectRoots / Tmpdir / SlashTmp / Unknown） |
| `FileSystemSandboxEntry` | 结构体 | 单条沙箱规则条目（路径 + 访问模式） |
| `FileSystemSandboxKind` | 枚举 | 沙箱类型（Restricted / Unrestricted / ExternalSandbox） |
| `FileSystemSandboxPolicy` | 结构体 | 完整的文件系统沙箱策略（类型 + 规则列表） |
| `FileSystemPath` | 枚举 | 路径表示（绝对路径或特殊路径） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_access_with_cwd` | `fn resolve_access_with_cwd(&self, path, cwd) -> FileSystemAccessMode` | 查询指定路径的有效访问权限 |
| `from_legacy_sandbox_policy` | `fn from_legacy_sandbox_policy(sandbox_policy, cwd) -> Self` | 从遗留策略转换为文件系统策略 |
| `to_legacy_sandbox_policy` | `fn to_legacy_sandbox_policy(&self, network, cwd) -> io::Result<SandboxPolicy>` | 反向转换回遗留策略 |
| `get_writable_roots_with_cwd` | `fn get_writable_roots_with_cwd(&self, cwd) -> Vec<WritableRoot>` | 计算可写根目录及其只读保护子路径 |
| `needs_direct_runtime_enforcement` | `fn needs_direct_runtime_enforcement(&self, network, cwd) -> bool` | 判断策略是否需要运行时直接执行（无法降级到遗留策略） |
| `has_full_disk_write_access` | `fn has_full_disk_write_access(&self) -> bool` | 判断是否具有全磁盘写权限 |
| `with_additional_writable_roots` | `fn with_additional_writable_roots(self, cwd, roots) -> Self` | 动态追加可写根目录 |

## 依赖关系

- **引用的 crate**: `codex_utils_absolute_path`, `schemars`, `serde`, `strum_macros`, `tracing`, `ts_rs`
- **被引用方**: `protocol.rs` 导出所有权限类型；`approvals.rs` 使用 `FileSystemSandboxPolicy`；`models.rs` 使用 `NetworkSandboxPolicy`

## 实现备注

- 访问权限冲突解析规则：最具体的路径优先，同一具体度下 `None > Write > Read`。
- `.git`、`.agents`、`.codex` 目录自动作为可写根目录的只读保护子路径。
- 支持 gitdir 指针文件（worktree/子模块场景）的自动解析和保护。
- 符号链接路径通过 `normalize_effective_absolute_path` 规范化，确保沙箱规则在符号链接环境下正确工作。
- 文件超过 2100 行（含大量测试），是 protocol crate 中最复杂的模块。
