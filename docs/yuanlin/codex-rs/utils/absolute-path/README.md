# absolute-path

## 功能概述

`codex-utils-absolute-path` 提供了一个保证路径为绝对且已规范化的类型 `AbsolutePathBuf`。该类型封装了标准库的 `PathBuf`，确保所有路径操作（创建、拼接、反序列化）都产生绝对路径，避免了在代码中传递未经验证的相对路径导致的错误。

核心特性：
- 自动展开 `~` 为用户主目录
- 支持相对路径基于指定基路径的解析
- 实现了 `Serialize` / `Deserialize`，反序列化时通过线程局部 guard 提供基路径
- 实现了 `JsonSchema` 和 `TS`（TypeScript 类型生成）

## 目录结构

```
absolute-path/
├── Cargo.toml
└── src/
    └── lib.rs          # AbsolutePathBuf 类型及其全部实现
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `dirs` | 获取用户主目录以展开 `~` |
| `path-absolutize` | 路径绝对化和规范化 |
| `schemars` | JSON Schema 生成 |
| `serde` | 序列化/反序列化支持 |
| `ts-rs` | TypeScript 类型定义生成 |

## 核心接口/API

### `AbsolutePathBuf`

保证绝对且规范化的路径类型。

```rust
// 从绝对路径创建
let path = AbsolutePathBuf::from_absolute_path("/usr/local/bin")?;

// 相对路径解析到基路径
let path = AbsolutePathBuf::resolve_path_against_base("file.txt", "/home/user")?;

// 相对于当前工作目录
let path = AbsolutePathBuf::relative_to_current_dir("src/main.rs")?;

// 获取当前目录
let cwd = AbsolutePathBuf::current_dir()?;

// 路径拼接
let child = path.join("subdir")?;

// 获取父目录
let parent = path.parent();
```

### `AbsolutePathBufGuard`

反序列化时使用的线程局部 guard，用于为反序列化过程提供基路径。

```rust
let _guard = AbsolutePathBufGuard::new(base_path);
let abs_path: AbsolutePathBuf = serde_json::from_str("\"relative/path\"")?;
// guard 在作用域结束时自动清除基路径
```

### Trait 实现

- `AsRef<Path>` / `Deref<Target = Path>` -- 可以透明地作为 `&Path` 使用
- `From<AbsolutePathBuf> for PathBuf` -- 转换为标准 PathBuf
- `TryFrom<&Path>` / `TryFrom<PathBuf>` / `TryFrom<&str>` / `TryFrom<String>` -- 各种来源的尝试转换
