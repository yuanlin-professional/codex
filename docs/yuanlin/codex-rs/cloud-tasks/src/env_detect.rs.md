# `env_detect.rs` — 环境自动检测模块

## 功能概述
实现 Codex Cloud 环境的自动检测逻辑：通过解析本地 Git 远程 URL 调用后端 by-repo API 查找关联环境，若无结果则回退到全局环境列表。支持 SSH 和 HTTPS 格式的 GitHub URL 解析。同时提供 TUI 环境列表加载功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AutodetectSelection` | 结构体 | 自动检测结果：环境 id 和可选 label |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `autodetect_environment_id` | `async fn(base_url, headers, label) -> Result<AutodetectSelection>` | 自动检测最佳环境 ID |
| `list_environments` | `async fn(base_url, headers) -> Result<Vec<EnvironmentRow>>` | 获取去重排序的环境列表 |

## 依赖关系
- **引用的 crate**: `codex_client`, `reqwest`, `serde`
- **被引用方**: TUI 事件循环中的环境初始化

## 实现备注
环境选择优先级：标签匹配 > 唯一环境 > pinned 环境 > 最高 task_count。
