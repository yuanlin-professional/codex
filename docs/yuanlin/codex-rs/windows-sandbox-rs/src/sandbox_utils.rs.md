# `sandbox_utils.rs` — Windows 沙盒共享工具函数

## 功能概述
该文件提供了 Windows 沙盒安装和运行时共用的工具函数，包括查找 Git 仓库根目录、确保 Codex home 目录存在，以及向沙盒用户环境注入 `git safe.directory` 配置，使沙盒用户能够在主用户拥有的仓库中执行 Git 命令。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `find_git_root` | `(start: &Path) -> Option<PathBuf>` | 向上查找 Git 工作树根目录 |
| `ensure_codex_home_exists` | `(p: &Path) -> Result<()>` | 确保 Codex home 目录存在 |
| `inject_git_safe_directory` | `(env_map, cwd)` | 注入 Git safe.directory 环境变量 |

## 依赖关系
- **引用的 crate**: `anyhow`, `dunce`
- **被引用方**: `elevated_impl`, 沙盒运行时
