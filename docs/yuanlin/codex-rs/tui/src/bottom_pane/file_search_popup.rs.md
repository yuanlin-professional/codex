# `file_search_popup.rs` -- 文件搜索弹出窗口

## 功能概述
该文件实现了文件搜索弹出窗口（FileSearchPopup），当用户在编辑器中输入 `@` 时显示。支持异步文件搜索、结果缓存、选择导航和查询更新。弹出窗口使用 fuzzy 匹配的高亮索引来标示匹配字符。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `FileSearchPopup` | 结构体 | 文件搜索弹出窗口状态，包含当前/待处理查询、匹配结果和滚动状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_query` | `fn set_query(&mut self, query: &str)` | 更新查询并进入等待状态 |
| `set_matches` | `fn set_matches(&mut self, query, matches)` | 接收搜索结果（仅接受与 pending_query 匹配的结果） |
| `set_empty_prompt` | `fn set_empty_prompt(&mut self)` | 设置空提示状态（仅显示 `@`） |
| `selected_match` | `fn selected_match(&self) -> Option<&PathBuf>` | 获取当前选中的文件路径 |
| `calculate_required_height` | `fn calculate_required_height(&self) -> u16` | 计算弹出窗口所需高度 |

## 依赖关系
- **引用的 crate**: `codex_file_search`（FileMatch）、`ratatui`
- **被引用方**: `ChatComposer` 持有 `FileSearchPopup` 实例

## 实现备注
- 结果数量限制为 `MAX_POPUP_ROWS`（8 条）
- 过期结果（query 不匹配 pending_query）被自动丢弃
- 等待搜索结果时显示 "loading..."，无结果时显示 "no matches"
- 匹配索引从 u32 转换为 usize 在 UI 边界处理
