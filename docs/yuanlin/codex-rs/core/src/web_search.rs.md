# `web_search.rs` — Web 搜索动作详情提取

该文件提供从 `WebSearchAction` 枚举中提取人类可读的搜索详情字符串的工具函数。`web_search_action_detail` 函数根据不同的动作类型（`Search`、`OpenPage`、`FindInPage`、`Other`）返回对应的描述文本：搜索类返回查询内容（多查询时追加省略号），打开页面类返回 URL，页面内查找类返回模式和 URL 的组合。`web_search_detail` 是顶层便利函数，在动作详情为空时退回到使用原始查询字符串。
