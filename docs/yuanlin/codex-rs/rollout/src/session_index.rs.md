# `session_index.rs` — 会话索引文件管理

## 功能概述
该文件管理 `session_index.jsonl` 追加式索引文件，用于存储线程 ID 与自定义名称的映射。支持追加名称更新、按名称查找线程 ID、按 ID 查找名称（单个和批量）、以及按名称查找 rollout 路径。索引从文件末尾扫描以获取最新条目。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionIndexEntry` | 结构体 | 索引条目：id + thread_name + updated_at |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `append_thread_name` | `async (codex_home, thread_id, name) -> io::Result<()>` | 追加名称更新 |
| `find_thread_name_by_id` | 函数 | 按 ID 查找最新名称 |
| `find_thread_names_by_ids` | 函数 | 批量按 ID 查找名称 |
| `find_thread_path_by_name_str` | 函数 | 按名称查找 rollout 路径 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `serde`, `tokio`
- **被引用方**: `recorder.rs`, CLI 命令

## 实现备注
- 使用从文件末尾向前扫描的策略，最新条目优先
- 索引文件为追加式，不修改已有条目
