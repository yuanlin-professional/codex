# `test_support.rs` — 状态运行时测试辅助工具

## 功能概述
该文件提供测试专用的辅助函数：`unique_temp_dir` 生成唯一的临时目录路径（使用纳秒时间戳 + UUID 避免冲突），`test_thread_metadata` 创建预填充的测试用 `ThreadMetadata` 实例。所有内容仅在 `#[cfg(test)]` 下编译。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 纯辅助函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `unique_temp_dir` | `() -> PathBuf` | 生成唯一临时目录路径 |
| `test_thread_metadata` | `(codex_home, thread_id, cwd) -> ThreadMetadata` | 创建测试用线程元数据 |

## 依赖关系
- **引用的 crate**: `uuid`, `chrono`, `codex_protocol`
- **被引用方**: 所有 `runtime/` 下的测试模块

## 实现备注
- 测试元数据使用固定时间戳 `1_700_000_000` 和 `gpt-5` 模型
