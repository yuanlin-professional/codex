# `realtime_context.rs` — 实时启动上下文构建器

## 功能概述

该文件实现了 Codex 的"实时启动上下文"（Realtime Startup Context）构建功能，在会话开始时动态组装一段背景信息注入到模型提示中。上下文包含三个核心部分：当前线程的最近对话回合（用于延续性）、跨工作区的近期工作摘要（从 state DB 加载的线程元数据）、以及本地工作区的目录树结构。所有内容均受 token 预算控制，经过截断后注入模型上下文。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| 无显著公开结构体 | - | 该模块主要由函数组成 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_realtime_startup_context` | `async fn(sess, budget_tokens) -> Option<String>` | 主入口：构建完整的实时启动上下文 |
| `build_current_thread_section` | `fn(items) -> Option<String>` | 从当前会话历史提取最近 2 个回合的用户/助手对话 |
| `build_recent_work_section` | `fn(cwd, recent_threads) -> Option<String>` | 构建近期工作摘要：按 Git 仓库分组、按时间排序 |
| `build_workspace_section_with_user_root` | `fn(cwd, user_root) -> Option<String>` | 构建工作区目录树：工作目录、Git 根、用户根 |
| `load_recent_threads` | `async fn(sess) -> Vec<ThreadMetadata>` | 从 state DB 加载最近 40 个线程的元数据 |
| `format_thread_group` | `fn(current_group, group, entries) -> Option<String>` | 格式化单个仓库/目录组的线程摘要 |
| `render_tree` | `fn(root) -> Option<Vec<String>>` | 渲染目录树（最大深度 2，每层最多 20 条目） |
| `format_section` | `fn(title, body, budget_tokens) -> Option<String>` | 格式化并截断单个章节 |

## 依赖关系

- **引用的 crate**: `codex_state`（线程元数据、state DB）、`codex_git_utils`（Git 项目根查找）、`codex_utils_output_truncation`（token 截断）、`dirs`（用户主目录）、`chrono`
- **被引用方**: Session 初始化时调用以构建初始上下文

## 实现备注

- 各个章节有独立的 token 预算：当前线程 1200、近期工作 2200、工作区 1600、备注 300。
- 目录树渲染过滤"噪声"目录（`.git`、`node_modules`、`target`、`build` 等），排除隐藏文件（以 `.` 开头），目录优先排列。
- 近期工作摘要按 Git 仓库分组，当前工作目录所在仓库置顶；每组显示最近会话数、最新活动时间、最新 Git 分支，以及去重后的用户提问列表。
- 当前工作目录组最多显示 8 条提问，其他组最多 5 条；每条提问截断到 240 字符。
- 提问文本经过空白规范化（`split_whitespace().join(" ")`）和重复检测（基于"cwd:ask"组合键去重）。
- 上下文头部明确标注："This is background context...may be incomplete or stale"，引导模型正确使用这些信息。
