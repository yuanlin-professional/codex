# `request_user_input_tool.rs` -- 用户输入请求工具定义

## 功能概述
定义 `request_user_input` 工具，允许 LLM 在需要用户决策时暂停并提供 2-3 个互斥选项供用户选择。选项包含 label（1-5 词）和 description（一句话说明影响/权衡），推荐选项标记为 "(Recommended)"。客户端会自动添加自由输入的 "Other" 选项。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_request_user_input_tool` | `fn(description) -> ToolSpec` | 创建用户输入请求工具 |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出
