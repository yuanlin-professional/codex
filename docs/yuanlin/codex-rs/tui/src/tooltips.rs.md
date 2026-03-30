# `tooltips.rs` — 启动提示和公告管理

## 功能概述

管理 TUI 启动时显示的提示信息。从本地提示文件和远程公告 TOML 文件中选取适当的提示。
根据用户计划类型（付费/免费/Go）、Fast 模式状态和平台，选择不同的推广提示。
远程公告支持版本匹配、日期范围过滤和目标应用筛选。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_tooltip` | `(plan, fast_mode) -> Option<String>` | 获取启动提示 |
| `announcement::prewarm` | `()` | 后台预加载远程公告 |
| `announcement::parse_announcement_tip_toml` | `(text) -> Option<String>` | 解析公告 TOML |

## 依赖关系

- **引用的 crate**: `codex_features`, `codex_protocol`, `rand`, `lazy_static`, `reqwest`, `toml`, `chrono`, `regex_lite`

## 实现备注

- 远程公告通过 `reqwest::blocking` 在独立线程中获取，避免阻塞主线程。
- 使用 `no_proxy()` 构建客户端以防止 macOS 系统代理检测导致的 panic。
- 提示选择策略：80% 概率显示推广提示（按用户类型），20% 概率显示随机通用提示。
- 公告 TOML 支持 `[[announcements]]` 数组格式，取最后一个匹配条目。
