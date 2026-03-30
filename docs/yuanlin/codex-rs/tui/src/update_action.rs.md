# `update_action.rs` — CLI 更新操作定义

## 功能概述

定义 TUI 退出后应执行的更新操作类型。根据当前安装方式（npm、bun、Homebrew）
检测应使用哪种更新命令，并提供命令行参数的字符串表示。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UpdateAction` | enum | 更新方式：NpmGlobalLatest/BunGlobalLatest/BrewUpgrade |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `command_args` | `(self) -> (&str, &[&str])` | 获取更新命令和参数 |
| `command_str` | `(self) -> String` | 获取更新命令的完整字符串 |
| `get_update_action` | `() -> Option<UpdateAction>` | 根据环境检测更新方式 |

## 依赖关系

- **引用的 crate**: `shlex`

## 实现备注

- 通过环境变量 `CODEX_MANAGED_BY_NPM`/`CODEX_MANAGED_BY_BUN` 检测包管理器。
- macOS 上检测可执行文件路径是否在 `/opt/homebrew` 或 `/usr/local` 下判断 Homebrew 安装。
- `get_update_action` 仅在 release 构建中可用。
