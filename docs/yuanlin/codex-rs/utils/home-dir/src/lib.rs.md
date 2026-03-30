# `lib.rs` — Codex 配置目录定位

## 功能概述

提供 `find_codex_home` 函数，返回 Codex 配置目录路径。优先使用 `CODEX_HOME` 环境变量（必须指向已存在的目录，并经过 canonicalize），未设置时默认返回 `~/.codex`。包含详尽的错误处理：路径不存在、不是目录、canonicalize 失败等均返回具有描述性消息的 `io::Error`。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `find_codex_home` | `fn() -> io::Result<PathBuf>` | 返回 Codex 配置目录路径 |

## 依赖关系

- **引用的 crate**: `dirs`
- **被引用方**: 配置加载模块

## 实现备注

- 内部函数 `find_codex_home_from_env` 接受可选字符串参数，便于测试。
- 测试覆盖了缺失路径、文件路径、有效目录和未设置环境变量四种场景。
