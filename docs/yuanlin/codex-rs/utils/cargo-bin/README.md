# cargo-bin

## 功能概述

`codex-utils-cargo-bin` 用于在测试代码中透明地定位构建产物的二进制文件路径。它同时支持 Cargo 和 Bazel 两种构建系统：
- 在 `cargo test` 下通过 `CARGO_BIN_EXE_*` 环境变量定位
- 在 `bazel test` 下通过 runfiles 机制解析

此外还提供了 `find_resource!` 宏用于定位测试资源文件，以及 `repo_root()` 函数用于获取仓库根目录。

## 目录结构

```
cargo-bin/
├── Cargo.toml
└── src/
    └── lib.rs          # cargo_bin()、find_resource! 宏、repo_root() 等
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `assert_cmd` | Cargo 二进制文件查找的回退方案 |
| `runfiles` | Bazel runfiles 解析 |
| `thiserror` | 错误类型派生 |

## 核心接口/API

### `cargo_bin(name: &str) -> Result<PathBuf, CargoBinError>`

返回当前测试运行中构建的二进制文件的绝对路径。查找顺序：
1. `CARGO_BIN_EXE_{name}` 环境变量
2. `CARGO_BIN_EXE_{name_with_underscores}` 环境变量（Cargo 会将连字符替换为下划线）
3. 通过 `assert_cmd::Command::cargo_bin` 回退查找

### `find_resource!($resource)` 宏

在运行时推导测试资源文件的路径。在 Cargo 下使用 `CARGO_MANIFEST_DIR`，在 Bazel 下使用 runfiles 机制。

```rust
let path = find_resource!("testdata/sample.json");
```

### `repo_root() -> io::Result<PathBuf>`

返回代码仓库的根目录路径。

### `runfiles_available() -> bool`

检测当前是否运行在 Bazel runfiles 环境中。

### `CargoBinError`

错误枚举类型，包含：
- `CurrentExe` -- 无法读取当前可执行文件
- `CurrentDir` -- 无法读取当前目录
- `ResolvedPathDoesNotExist` -- 解析后的路径不存在
- `NotFound` -- 无法定位指定二进制文件
