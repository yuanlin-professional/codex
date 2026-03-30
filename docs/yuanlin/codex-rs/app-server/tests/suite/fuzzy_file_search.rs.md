# `fuzzy_file_search.rs` — 测试模块

## 测试覆盖范围
测试模糊文件搜索功能的一次性请求模式和会话（session）流式模式，涵盖排序、评分、
取消令牌、会话生命周期管理、多查询更新、停止后行为以及多会话独立性等场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `test_fuzzy_file_search_sorts_and_includes_indices` | 搜索结果按评分排序且包含匹配索引 |
| `test_fuzzy_file_search_accepts_cancellation_token` | 支持取消令牌参数 |
| `test_fuzzy_file_search_session_streams_updates` | 会话模式流式推送搜索更新通知 |
| `test_fuzzy_file_search_session_no_updates_after_complete_until_query_edited` | 搜索完成后不再推送更新，直到查询被修改 |
| `test_fuzzy_file_search_session_update_before_start_errors` | 会话未启动时更新查询返回错误 |
| `test_fuzzy_file_search_session_update_works_without_waiting_for_start_response` | 不等待 start 响应也可发送 update 请求 |
| `test_fuzzy_file_search_session_multiple_query_updates_work` | 多次查询更新正常工作 |
| `test_fuzzy_file_search_session_update_after_stop_fails` | 会话停止后更新查询返回错误 |
| `test_fuzzy_file_search_session_stops_sending_updates_after_stop` | 会话停止后不再发送更新通知 |
| `test_fuzzy_file_search_two_sessions_are_independent` | 两个独立会话互不影响 |
| `test_fuzzy_file_search_query_cleared_sends_blank_snapshot` | 清空查询时发送空结果快照 |
