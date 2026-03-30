# `recorder.rs` — Rollout 文件录制器

## 功能概述
`RolloutRecorder` 负责将 Codex 会话事件持久化为 JSONL 格式的 rollout 文件。支持异步写入、会话元数据追加、线程列表查询、归档/取消归档操作、以及与 StateDb 的增量同步。使用 mpsc 通道和后台 tokio 任务实现非阻塞写入。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RolloutRecorder` | 结构体 | Rollout 文件录制器 |
| `RolloutRecorderParams` | 结构体 | 录制器初始化参数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RolloutRecorder::new` | 未完全展示 | 创建录制器并打开 rollout 文件 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_git_utils`, `tokio`, `serde_json`
- **被引用方**: `codex-core` 会话管理

## 实现备注
- 使用日期分区的目录结构存储 rollout 文件
