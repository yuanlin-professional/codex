# `paste_burst.rs` -- 粘贴突发检测状态机

## 功能概述
该文件实现了粘贴突发检测状态机（PasteBurst），用于在不支持括号粘贴的终端上检测和处理粘贴操作。在这些终端中，粘贴内容会以快速连续的 KeyCode::Char 事件形式到达，状态机通过时间间隔启发式方法将这些事件识别为粘贴操作，避免触发绑定到特定字符的副作用（如 `?` 触发快捷键），并确保粘贴中的 Enter 作为换行而非提交处理。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PasteBurst` | 结构体 | 粘贴突发检测状态机，包含时间戳、缓冲区和活跃标志 |
| `CharDecision` | 枚举 | 字符处理决策：BeginBuffer、BufferAppend、RetainFirstChar、BeginBufferFromPending |
| `RetroGrab` | 结构体 | 回溯捕获结果，包含起始字节位置和已捕获的文本 |
| `FlushResult` | 枚举 | 刷新结果：Paste(String)、Typed(char)、None |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `on_plain_char` | `fn on_plain_char(&mut self, ch, now) -> CharDecision` | ASCII 字符入口，可能保留首字符 |
| `on_plain_char_no_hold` | `fn on_plain_char_no_hold(&mut self, now) -> Option<CharDecision>` | 非 ASCII 字符入口，不保留首字符 |
| `flush_if_due` | `fn flush_if_due(&mut self, now) -> FlushResult` | 超时后刷新缓冲区 |
| `decide_begin_buffer` | `fn decide_begin_buffer(&mut self, now, before, retro_chars) -> Option<RetroGrab>` | 决定是否回溯捕获已输入文本 |
| `append_newline_if_active` | `fn append_newline_if_active(&mut self, now) -> bool` | 活跃状态下将 Enter 作为换行追加到缓冲区 |

## 依赖关系
- **引用的 crate**: `std::time`
- **被引用方**: `ChatComposer` 持有 `PasteBurst` 实例

## 实现备注
- 两个时间阈值：字符间隔（非 Windows 8ms，Windows 30ms）和活跃空闲超时（非 Windows 8ms，Windows 60ms）
- 最少 3 个连续快速字符才触发突发检测
- 回溯捕获仅在已输入文本"看起来像粘贴"时启用（包含空格或>=16 字符）
- Enter 抑制窗口在突发缓冲区刷新后仍保持 120ms，防止尾部 Enter 被误判为提交
