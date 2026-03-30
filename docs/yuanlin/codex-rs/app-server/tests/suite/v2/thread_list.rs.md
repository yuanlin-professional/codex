# `thread_list.rs` — 测试模块

## 测试覆盖范围
全面测试 `thread/list` JSON-RPC 方法，涵盖空列表、失败 turn 后的错误标志、分页、
提供者过滤、cwd 过滤、搜索词过滤、来源类型过滤（交互/子代理）、子代理变体过滤、
限制数量逻辑、最大限制强制、不足结果处理、git 信息包含、排序方式（created_at/updated_at）、
分页游标和排序平局处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_list_basic_empty` | 空目录返回空列表 |
| `thread_list_reports_system_error_idle_flag_after_failed_turn` | 失败 turn 后报告系统错误标志 |
| `thread_list_pagination_next_cursor_none_on_last_page` | 最后一页游标为空 |
| `thread_list_respects_provider_filter` | 提供者过滤生效 |
| `thread_list_respects_cwd_filter` | 工作目录过滤生效 |
| `thread_list_respects_search_term_filter` | 搜索词过滤生效 |
| `thread_list_empty_source_kinds_defaults_to_interactive_only` | 空来源类型默认仅交互会话 |
| `thread_list_filters_by_source_kind_subagent_thread_spawn` | 按子代理来源过滤 |
| `thread_list_filters_by_subagent_variant` | 按子代理变体过滤 |
| `thread_list_fetches_until_limit_or_exhausted` | 持续获取直到满足限制或耗尽 |
| `thread_list_enforces_max_limit` | 强制最大限制 |
| `thread_list_stops_when_not_enough_filtered_results_exist` | 过滤后结果不足时停止 |
| `thread_list_includes_git_info` | 包含 git 信息 |
| `thread_list_default_sorts_by_created_at` | 默认按创建时间排序 |
| `thread_list_sort_updated_at_orders_by_mtime` | updated_at 排序按修改时间 |
| `thread_list_updated_at_paginates_with_cursor` | updated_at 排序支持游标分页 |
| `thread_list_created_at_tie_breaks_by_uuid` | created_at 排序平局时按 UUID 排序 |
| `thread_list_updated_at_tie_breaks_by_uuid` | updated_at 排序平局时按 UUID 排序 |
