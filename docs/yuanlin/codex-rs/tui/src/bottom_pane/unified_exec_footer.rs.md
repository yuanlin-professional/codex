# `unified_exec_footer.rs` -- 统一执行会话页脚

## 功能概述
该文件实现了统一执行会话摘要页脚（UnifiedExecFooter），用于显示后台终端会话的状态摘要。提供一个规范的摘要字符串，底部面板可以将其作为独立页脚行渲染，也可以内联到状态行中，避免重复文案/语法逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `UnifiedExecFooter` | 结构体 | 跟踪活跃的统一执行进程并渲染紧凑摘要 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new() -> Self` | 创建空实例 |
| `set_processes` | `fn set_processes(&mut self, processes) -> bool` | 更新进程列表，返回是否有变化 |
| `is_empty` | `fn is_empty(&self) -> bool` | 检查是否为空 |
| `summary_text` | `fn summary_text(&self) -> Option<String>` | 生成摘要文本（如 "1 background terminal running · /ps to view · /stop to close"） |

## 依赖关系
- **引用的 crate**: `ratatui`
- **被引用方**: `BottomPane` 持有 `UnifiedExecFooter` 实例

## 实现备注
- 摘要文本不含前导空格和分隔符，由调用方决定布局
- 当状态指示器可见时，摘要内联到状态行中；否则作为独立页脚行
- 使用 `take_prefix_by_width` 在窄终端中截断显示
- 正确处理单复数（terminal/terminals）
