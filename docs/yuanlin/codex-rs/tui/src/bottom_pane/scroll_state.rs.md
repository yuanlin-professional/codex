# `scroll_state.rs` -- 通用滚动/选择状态

## 功能概述
该文件实现了通用的垂直列表滚动和选择状态管理（ScrollState），被多个弹出窗口和选择列表共享使用。支持可选选择（空列表时为 None）、环绕导航（Up/Down 到达边界时循环）和维护滚动窗口确保选中行始终可见。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ScrollState` | 结构体 | 包含 `selected_idx: Option<usize>` 和 `scroll_top: usize` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `clamp_selection` | `fn clamp_selection(&mut self, len: usize)` | 将选择限制在 [0, len-1] 范围内 |
| `move_up_wrap` | `fn move_up_wrap(&mut self, len: usize)` | 上移选择，到顶部时环绕到底部 |
| `move_down_wrap` | `fn move_down_wrap(&mut self, len: usize)` | 下移选择，到底部时环绕到顶部 |
| `ensure_visible` | `fn ensure_visible(&mut self, len, visible_rows)` | 调整 scroll_top 使选中行在可见窗口内 |
| `reset` | `fn reset(&mut self)` | 重置选择和滚动位置 |

## 依赖关系
- **引用的 crate**: 无外部依赖
- **被引用方**: CommandPopup、FileSearchPopup、SkillPopup、ListSelectionView、ExperimentalFeaturesView 等

## 实现备注
- 空列表时 selected_idx 设为 None，scroll_top 设为 0
- ensure_visible 仅在 selected_idx 超出当前可见窗口时调整 scroll_top
