# `mod.rs` -- 记忆子系统模块入口与配置常量

## 功能概述

该文件是记忆子系统的模块入口，定义了两阶段记忆管线（Phase 1: 提取、Phase 2: 合并）的所有配置常量、指标名称和目录布局工具函数。Phase 1 负责从会话 rollout 中提取原始记忆，Phase 2 负责将多条原始记忆合并整理。模块还声明了子模块的可见性并导出核心入口函数 `start_memories_startup_task` 和 `clear_memory_root_contents`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `artifacts` | 常量模块 | 定义产物子目录名 `rollout_summaries` 和原始记忆文件名 `raw_memories.md` |
| `phase_one` | 常量模块 | Phase 1 的默认模型、推理强度、并发限制、token 限额、作业租约等配置 |
| `phase_two` | 常量模块 | Phase 2 的默认模型、推理强度、作业租约、心跳间隔等配置 |
| `metrics` | 常量模块 | OpenTelemetry 指标名称，涵盖 Phase 1/2 的作业计数、延迟、token 使用量 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `memory_root` | `fn(&Path) -> PathBuf` | 返回 `codex_home/memories` 路径 |
| `rollout_summaries_dir` | `fn(&Path) -> PathBuf` | 返回 rollout 摘要子目录路径 |
| `raw_memories_file` | `fn(&Path) -> PathBuf` | 返回 `raw_memories.md` 文件路径 |
| `ensure_layout` | `async fn(&Path) -> io::Result<()>` | 确保目录结构存在（含 rollout_summaries 子目录） |

## 依赖关系

- **引用的 crate**: `codex_protocol::openai_models::ReasoningEffort`、`std::path`、`tokio::fs`
- **被引用方**: 记忆子系统内的所有子模块以及 `codex` 主入口

## 实现备注

- Phase 1 默认使用 `gpt-5.1-codex-mini` 模型，Phase 2 使用 `gpt-5.3-codex` 模型。
- Phase 1 使用 70% 的上下文窗口比例来为系统指令和输出预留空间。
- 作业租约默认为 3600 秒（1 小时），Phase 2 心跳间隔为 90 秒。
- 线程扫描上限为 5000，修剪批次大小为 200。
