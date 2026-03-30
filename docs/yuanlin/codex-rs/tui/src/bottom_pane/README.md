# src/bottom_pane/

## 功能概述

`bottom_pane/` 模块是 TUI 聊天界面的交互式底部面板。它拥有 `ChatComposer`（可编辑的提示输入框）和一个瞬态视图栈（`BottomPaneView`），用于临时替换输入框以进行集中交互，如审批覆盖层、命令弹窗、文件搜索、技能选择等。

输入路由采用分层设计：`BottomPane` 决定哪个本地表面接收按键（视图 vs 输入框），而更高层级的意图（如"中断"或"退出"）由父组件 `ChatWidget` 决定。

## 目录结构

```
bottom_pane/
├── mod.rs                      # 模块入口，BottomPane 主结构体和视图栈管理
├── approval_overlay.rs         # 命令执行审批覆盖层
├── app_link_view.rs            # 应用链接视图
├── bottom_pane_view.rs         # BottomPaneView 枚举定义
├── chat_composer.rs            # ChatComposer 文本输入组件
├── chat_composer_history.rs    # 输入历史记录
├── command_popup.rs            # 命令弹窗
├── custom_prompt_view.rs       # 自定义提示视图
├── experimental_features_view.rs # 实验性功能视图
├── feedback_view.rs            # 反馈视图
├── file_search_popup.rs        # 文件搜索弹窗
├── footer.rs                   # 底部状态栏
├── list_selection_view.rs      # 通用列表选择视图
├── mcp_server_elicitation.rs   # MCP 服务器请求表单覆盖层
├── multi_select_picker.rs      # 多选选择器
├── paste_burst.rs              # 粘贴突发检测
├── pending_input_preview.rs    # 待处理输入预览
├── pending_thread_approvals.rs # 待处理线程审批
├── popup_consts.rs             # 弹窗常量
├── prompt_args.rs              # 提示参数
├── request_user_input/         # 用户输入请求覆盖层
├── scroll_state.rs             # 滚动状态
├── selection_popup_common.rs   # 选择弹窗通用工具
├── skill_popup.rs              # 技能弹窗
├── skills_toggle_view.rs       # 技能开关视图
├── slash_commands.rs           # 斜杠命令处理
├── status_line_setup.rs        # 状态行配置
├── textarea.rs                 # 文本域组件
├── title_setup.rs              # 标题配置
├── unified_exec_footer.rs      # 统一执行底栏
└── snapshots/                  # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `BottomPane` | 底部面板主结构体，管理输入框和视图栈 |
| `ChatComposer` | 可编辑文本输入组件，支持多行、粘贴、历史记录 |
| `BottomPaneView` | 瞬态视图枚举（审批、选择列表、文件搜索等） |
| `ApprovalOverlay` / `ApprovalRequest` | 命令执行审批 UI |
| `McpServerElicitationOverlay` | MCP 服务器请求表单 UI |
| `RequestUserInputOverlay` | 用户输入请求覆盖层 |
| `SelectionViewParams` / `SelectionItem` | 通用列表选择接口 |
| `AppLinkView` | 应用链接交互视图 |
| `FeedbackAudience` | 反馈目标受众类型 |
