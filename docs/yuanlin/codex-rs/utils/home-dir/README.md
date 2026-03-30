# home-dir

## 功能概述

`codex-utils-home-dir` 负责定位 Codex 的配置主目录。支持通过 `CODEX_HOME` 环境变量自定义配置目录位置，默认使用 `~/.codex`。

行为规则：
- 若设置了 `CODEX_HOME`，必须指向一个已存在的目录，返回其 canonicalize 后的路径
- 若 `CODEX_HOME` 指向不存在的路径或非目录，返回错误
- 若未设置 `CODEX_HOME`，返回 `~/.codex`（不验证是否存在）

## 目录结构

```
home-dir/
├── Cargo.toml
└── src/
    └── lib.rs          # find_codex_home() 函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `dirs` | 获取系统用户主目录 |

## 核心接口/API

### `find_codex_home() -> std::io::Result<PathBuf>`

返回 Codex 配置目录的路径。

```rust
let home = find_codex_home()?;
// 默认返回 ~/.codex
// 或返回 CODEX_HOME 环境变量指定的目录
```

常见错误情况：
- `NotFound` -- `CODEX_HOME` 指向的路径不存在
- `InvalidInput` -- `CODEX_HOME` 指向的路径不是目录
- `NotFound` -- 无法确定用户主目录
