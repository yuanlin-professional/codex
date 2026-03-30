# `skill_popup.rs` -- 技能/提及弹出窗口

## 功能概述
该文件实现了技能/应用提及弹出窗口（SkillPopup），当用户在编辑器中输入 `$` 时显示可用的技能和应用列表。支持模糊搜索过滤、多搜索词匹配、分类标签显示（如 [Skill]、[App]）和排序优先级。名称超过 24 字符时自动截断。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MentionItem` | 结构体 | 提及项目数据，包含显示名称、描述、插入文本、搜索词、路径、分类标签和排序优先级 |
| `SkillPopup` | 结构体 | 技能弹出窗口状态，包含查询字符串、提及列表和滚动状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(mentions: Vec<MentionItem>) -> Self` | 创建弹出窗口 |
| `set_query` | `fn set_query(&mut self, query: &str)` | 更新搜索查询 |
| `selected_mention` | `fn selected_mention(&self) -> Option<&MentionItem>` | 获取选中的提及项 |
| `filtered` | `fn filtered(&self) -> Vec<(usize, Option<Vec<usize>>, i32)>` | 执行模糊匹配并按优先级排序 |

## 依赖关系
- **引用的 crate**: `codex_utils_fuzzy_match`、`ratatui`
- **被引用方**: `ChatComposer` 持有 `SkillPopup` 实例

## 实现备注
- 排序优先级：sort_rank > 模糊匹配得分 > 字母序
- 匹配尝试顺序：display_name > search_terms 中的其他词
- 使用 `render_rows_single_line` 进行单行渲染（不换行）
- 页脚显示 Enter 插入和 Esc 关闭提示
