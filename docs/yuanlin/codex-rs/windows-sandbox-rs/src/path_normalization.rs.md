# `path_normalization.rs` -- 路径规范化工具

## 功能概述

该文件提供 Windows 路径规范化工具函数。`canonicalize_path` 使用 `dunce::canonicalize` 去除 Windows 的 `\\?\` 前缀进行路径规范化。`canonical_path_key` 进一步将路径转为小写并统一使用正斜杠分隔符，生成可用于比较的规范化键值。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件无结构体定义 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `canonicalize_path` | `fn(path: &Path) -> PathBuf` | 使用 dunce 规范化路径，失败时返回原始路径 |
| `canonical_path_key` | `fn(path: &Path) -> String` | 规范化路径后转小写并将反斜杠替换为正斜杠 |

## 依赖关系

- **引用的 crate**: `dunce`
- **被引用方**: `workspace_acl.rs`

## 实现备注

- 包含测试验证 Windows 风格路径（`C:\Users\Dev\Repo`）和斜杠风格路径（`c:/users/dev/repo`）规范化后相等
