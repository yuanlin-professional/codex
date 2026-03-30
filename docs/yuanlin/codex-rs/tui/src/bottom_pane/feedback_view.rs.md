# `feedback_view.rs` -- 反馈收集视图

## 功能概述
该文件实现了用户反馈收集的完整流程，包括反馈分类选择（FeedbackCategory）、日志上传同意、反馈备注输入（FeedbackNoteView）以及上传完成后的跟进链接展示。支持五种反馈类别：BadResult、GoodResult、Bug、SafetyCheck 和 Other。根据用户身份（OpenAI 员工或外部用户）显示不同的跟进链接。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `FeedbackAudience` | 枚举 | 反馈受众：OpenAiEmployee 或 External |
| `FeedbackNoteView` | 结构体 | 反馈备注输入视图，包含分类、快照、日志路径等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `feedback_selection_params` | `fn feedback_selection_params(tx) -> SelectionViewParams` | 构建反馈分类选择弹出窗口参数 |
| `feedback_upload_consent_params` | `fn feedback_upload_consent_params(...) -> SelectionViewParams` | 构建日志上传同意弹出窗口参数 |
| `feedback_disabled_params` | `fn feedback_disabled_params() -> SelectionViewParams` | 构建反馈禁用提示弹出窗口参数 |
| `submit` | `fn submit(&mut self)` | 上传反馈并显示结果信息（包含线程 ID 和跟进链接） |

## 依赖关系
- **引用的 crate**: `codex_feedback`（FeedbackSnapshot, FeedbackDiagnostics）、`codex_protocol`（SessionSource）、`crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出多个公共函数和类型供上层使用

## 实现备注
- 反馈上传通过 `codex_feedback` crate 的 `upload_feedback` 方法实现
- 支持连接诊断信息展示（代理设置、自定义 base URL 等）
- 内部员工和外部用户看到不同的跟进链接（Slack vs GitHub Issues）
- Bug 报告的 GitHub Issue URL 自动填充线程 ID
