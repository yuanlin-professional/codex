# `pending_input_preview.rs` -- 待处理输入预览组件

## 功能概述
该文件实现了待处理输入预览组件（PendingInputPreview），在编辑器上方显示三类待处理消息：待提交的引导消息（pending steers，在下次工具调用后提交）、被拒绝的引导消息（rejected steers，在回合结束时重新提交）和排队的后续消息（queued messages）。每类消息限制显示 3 行并支持自适应换行。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PendingInputPreview` | 结构体 | 持有三类待处理消息列表和编辑快捷键绑定 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new() -> Self` | 创建空的预览实例 |
| `set_edit_binding` | `fn set_edit_binding(&mut self, binding)` | 设置编辑最后排队消息的快捷键显示 |
| `as_renderable` | `fn as_renderable(&self, width) -> Box<dyn Renderable>` | 生成可渲染对象 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`
- **被引用方**: `BottomPane` 持有 `PendingInputPreview` 实例

## 实现备注
- 每条消息最多显示 3 行，超出用省略号 `…` 表示
- 各区块间用空行分隔
- 排队消息底部显示编辑快捷键提示（默认 Alt+Up）
- 使用 `adaptive_wrap_lines` 进行自适应换行
