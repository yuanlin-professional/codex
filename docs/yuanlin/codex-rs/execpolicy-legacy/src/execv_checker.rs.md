# `execv_checker.rs` — exec 调用的路径和权限校验器

## 功能概述

`ExecvChecker` 是策略执行的核心组件，负责两个层面的校验：首先调用 `Policy::check` 进行命令模式匹配，然后对匹配后的参数中涉及的文件路径进行读写权限目录校验。确保可读文件位于允许的读目录下，可写文件位于允许的写目录下。还负责解析系统路径以选择最安全的可执行文件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecvChecker` | 结构体 | 封装了一个 `Policy` 实例，提供匹配和权限校验两步检查 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(execv_policy: Policy) -> Self` | 使用给定策略创建检查器实例 |
| `match` | `(&self, exec_call: &ExecCall) -> Result<MatchedExec>` | 第一步：对命令进行策略模式匹配 |
| `check` | `(&self, valid_exec, cwd, readable_folders, writeable_folders) -> Result<String>` | 第二步：验证匹配后的文件路径是否在允许的目录范围内，并返回推荐的可执行文件路径 |
| `ensure_absolute_path` | `(path, cwd) -> Result<PathBuf>` | 将相对路径转换为绝对路径 |
| `is_executable_file` | `(path: &str) -> bool` | 检查文件是否存在且具有可执行权限 |

## 依赖关系

- **引用的 crate**: `path_absolutize`
- **被引用方**: 作为 `lib.rs` 的公开导出项，供外部使用

## 实现备注

- 使用宏 `check_file_in_folders!` 来简化文件路径与目录列表的包含关系检查。
- 文件中包含了完整的单元测试，使用临时目录和伪造的可执行文件进行端到端验证。
- 跨平台支持：Unix 上检查可执行位，Windows 上依赖文件扩展名（TODO 标注）。
