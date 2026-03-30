# `rollout.rs` — 模拟 Rollout 文件创建工具

## 功能概述
提供用于在测试中创建模拟 rollout（会话记录）JSONL 文件的函数。rollout 文件按 `CODEX_HOME/sessions/YYYY/MM/DD/`
目录结构存储。每个 rollout 包含 session_meta、response_item 和 event_msg 三行 JSON 数据。
支持自定义会话源（CLI/其他）、模型提供者、git 信息和文本元素等参数。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `rollout_path` | `pub fn rollout_path(codex_home, filename_ts, thread_id) -> PathBuf` | 计算 rollout 文件的存储路径 |
| `create_fake_rollout` | `pub fn create_fake_rollout(codex_home, filename_ts, meta_rfc3339, preview, ...) -> Result<String>` | 创建基本的伪造 rollout 文件，返回 UUID |
| `create_fake_rollout_with_source` | `pub fn ...(... source: SessionSource) -> Result<String>` | 创建指定会话来源的 rollout 文件 |
| `create_fake_rollout_with_text_elements` | `pub fn ...(... text_elements: Vec<Value>, ...) -> Result<String>` | 创建包含自定义文本元素的 rollout 文件 |

## 依赖关系
- **引用的 crate**: `anyhow`, `codex_protocol`, `serde_json`, `uuid`, `chrono`
- **被引用方**: `common/lib.rs` 导出，供 thread_list、conversation_summary、thread_resume 等测试使用

## 实现备注
文件修改时间（mtime）会被设置为 `meta_rfc3339` 指定的时间戳，用于模拟真实的文件时间排序。
