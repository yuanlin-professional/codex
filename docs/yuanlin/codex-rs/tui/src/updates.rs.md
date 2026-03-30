# `updates.rs` — 版本更新检查

## 功能概述

后台检查 Codex CLI 的最新版本，并缓存结果到 `{codex_home}/version.json`。
支持 GitHub Releases API 和 Homebrew Cask API 两种版本源。
提供版本比较、弹出提示过滤（尊重用户的版本跳过偏好）和版本跳过持久化。
仅在 release 构建中编译。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_upgrade_version` | `(config) -> Option<String>` | 获取可升级版本（后台刷新缓存） |
| `get_upgrade_version_for_popup` | `(config) -> Option<String>` | 获取弹窗可用的版本（尊重跳过） |
| `dismiss_version` | `async (config, version) -> Result<()>` | 持久化版本跳过决定 |

## 依赖关系

- **引用的 crate**: `chrono`, `codex_core`, `reqwest`, `serde`, `serde_json`

## 实现备注

- 版本检查每 20 小时最多执行一次网络请求，使用 `tokio::spawn` 后台运行。
- 当前运行使用上次缓存的结果，下次运行才显示新检测到的版本。
- Homebrew 安装使用 Cask API 获取版本（可能滞后于 GitHub Release）。
- 版本比较为纯数字 semver（major.minor.patch），不支持预发布标签。
