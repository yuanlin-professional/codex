# path-utils

## 功能概述

`codex-utils-path`（目录名 `path-utils`）提供了路径规范化、符号链接解析、原子文件写入以及环境检测等跨平台路径处理工具。

核心功能：
- **路径规范化** -- 将路径 canonicalize 并处理 WSL 大小写不敏感的 Windows 挂载路径
- **符号链接解析** -- 跟踪符号链接链直到真实文件，检测循环链接
- **原子写入** -- 通过临时文件 + rename 确保文件写入的原子性
- **环境检测** -- 检测 WSL 环境和无头（headless）环境

## 目录结构

```
path-utils/
├── Cargo.toml
└── src/
    ├── lib.rs              # 主要路径工具函数
    ├── env.rs              # 环境检测（WSL、headless）
    └── path_utils_tests.rs # 测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-utils-absolute-path` | `AbsolutePathBuf` 类型 |
| `dunce` | Windows 路径简化（去除 `\\?\` 前缀） |
| `tempfile` | 原子写入的临时文件 |

## 核心接口/API

### `normalize_for_path_comparison(path) -> io::Result<PathBuf>`

将路径 canonicalize 后进行 WSL 规范化，适用于路径比较场景。

### `normalize_for_native_workdir(path) -> PathBuf`

为原生工作目录规范化路径。Windows 下使用 `dunce::simplified` 去除 `\\?\` 前缀。

### `resolve_symlink_write_paths(path: &Path) -> io::Result<SymlinkWritePaths>`

解析符号链接链到最终目标，返回读路径和写路径。检测循环链接，元数据/解析失败时回退到原始路径。

```rust
pub struct SymlinkWritePaths {
    pub read_path: Option<PathBuf>,  // 可读取的最终路径（失败时为 None）
    pub write_path: PathBuf,          // 安全的写入路径
}
```

### `write_atomically(write_path: &Path, contents: &str) -> io::Result<()>`

通过在同目录创建临时文件再 rename 的方式实现原子写入，确保不会产生损坏的中间状态。

### `env` 模块

```rust
pub fn is_wsl() -> bool;                // 是否运行在 WSL 下
pub fn is_headless_environment() -> bool; // 是否为无头环境（CI/SSH/无 DISPLAY）
```
