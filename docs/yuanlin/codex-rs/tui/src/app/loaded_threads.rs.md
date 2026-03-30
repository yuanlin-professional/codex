# `loaded_threads.rs` — 子代理线程的生成树发现

## 功能概述

当 TUI 恢复或切换到已有线程时，需要发现属于主线程的所有子代理线程。本模块提供纯同步的树遍历算法，从 app-server 返回的扁平线程列表中筛选出主线程的所有后代。遍历从 `primary_thread_id` 开始，沿 `SessionSource::SubAgent(ThreadSpawn)` 边进行广度优先搜索。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LoadedSubagentThread` | struct | 发现的子代理线程，携带 thread_id、agent_nickname 和 agent_role |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `find_loaded_subagent_threads_for_primary` | `(threads, primary_id) -> Vec<LoadedSubagentThread>` | 广度优先搜索生成树，返回主线程的所有后代子代理 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol`, `codex_protocol`
- **被引用方**: `App` 在恢复/切换线程时填充 `AgentNavigationState`

## 实现备注

- 纯同步实现，无 async、无 I/O、无副作用，可独立单元测试
- 主线程本身不包含在输出中
- 结果按 thread_id 字符串排序以确保确定性输出
- 通过 `included` HashSet 防止重复访问（虽然理论上 UUID 不会形成环）
