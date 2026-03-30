# `lib.rs` — 保证绝对路径的类型封装

## 功能概述

本文件定义了 `AbsolutePathBuf` 类型，它是一个封装了 `PathBuf` 的新类型，保证内部路径始终为绝对路径且经过规范化处理（但不保证存在于文件系统中或经过 canonicalize）。该类型支持从相对路径基于给定 base 路径进行解析、展开 `~` 到用户主目录，以及通过 serde 反序列化时利用线程局部存储的 `AbsolutePathBufGuard` 来提供 base 路径。此外还提供多种 trait 实现以便与标准路径类型无缝互操作。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AbsolutePathBuf` | struct | 封装 `PathBuf`，保证路径为绝对且规范化 |
| `AbsolutePathBufGuard` | struct | RAII 守卫，设置线程局部 base 路径用于反序列化 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_path_against_base` | `fn(path, base_path) -> io::Result<Self>` | 将路径相对于 base 解析为绝对路径 |
| `from_absolute_path` | `fn(path) -> io::Result<Self>` | 从已有路径构造绝对路径 |
| `current_dir` | `fn() -> io::Result<Self>` | 获取当前工作目录的绝对路径 |
| `relative_to_current_dir` | `fn(path) -> io::Result<Self>` | 相对于进程当前目录解析路径 |
| `join` | `fn(&self, path) -> io::Result<Self>` | 在当前路径下连接子路径 |
| `maybe_expand_home_directory` | `fn(path) -> PathBuf` | 展开 `~` 前缀为用户主目录 |

## 依赖关系

- **引用的 crate**: `dirs`, `path_absolutize`, `schemars`, `serde`, `ts_rs`
- **被引用方**: `codex-rs` 项目中需要可靠绝对路径的所有 crate（如 `path-utils`, `sandbox-summary` 等）

## 实现备注

- 反序列化时需要通过 `AbsolutePathBufGuard::new(base)` 设置线程局部 base 路径；未设置时仅接受已经为绝对的路径。
- 在 Windows 上支持 `~\` 反斜杠风格的主目录展开。
- 测试覆盖了绝对路径忽略 base、相对路径解析、守卫反序列化、`~` 展开（含双斜杠和 Windows 反斜杠场景）。
