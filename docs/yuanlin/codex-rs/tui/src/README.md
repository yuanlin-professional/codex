# src/

## 功能概述

`src/` 是 `codex-tui` crate 的源代码根目录。它包含库入口 `lib.rs`、二进制入口 `main.rs`，以及众多功能模块文件和子目录。

顶层模块文件涵盖了 TUI 的方方面面：应用生命周期、事件系统、Markdown 渲染、命令执行、剪贴板操作、颜色主题、差异渲染、文件搜索、会话日志、动画效果等。

## 目录结构

```
src/
├── main.rs                # codex-tui 二进制入口点
├── lib.rs                 # 库入口，核心启动和编排逻辑
├── app/                   # 应用主控逻辑（app server 适配、多 Agent 导航、线程管理）
├── bin/                   # 辅助二进制工具（md-events）
├── bottom_pane/           # 底部交互面板组件
├── chatwidget/            # 聊天界面主组件
├── exec_cell/             # 命令执行单元格
├── notifications/         # 桌面通知后端
├── onboarding/            # 引导流程（认证、目录信任）
├── public_widgets/        # 对外公开的可复用 widget
├── render/                # 渲染基础设施
├── status/                # 状态信息格式化
├── streaming/             # 流式文本处理
├── tui/                   # TUI 事件循环和终端管理
├── app.rs                 # App 主结构体，聊天会话主循环
├── app_event.rs           # 应用内部事件定义
├── app_event_sender.rs    # 事件发送器
├── app_command.rs         # 应用命令抽象
├── app_backtrack.rs       # 回退（undo）状态管理
├── app_server_session.rs  # App Server 会话封装
├── ascii_animation.rs     # ASCII 动画播放器
├── cli.rs                 # CLI 参数定义（clap）
├── chatwidget.rs          # ChatWidget 模块入口
├── markdown.rs            # Markdown 解析
├── markdown_render.rs     # Markdown 渲染为 ratatui Line
├── markdown_stream.rs     # 流式 Markdown 收集器
├── tui.rs                 # TUI 模块入口
├── frames.rs              # 动画帧常量定义
└── ...                    # 其他功能模块
```

## 核心接口/API

| 模块文件 | 说明 |
|----------|------|
| `lib.rs` | `run_main()` 主入口、`AppExitInfo`/`ExitReason` 类型导出 |
| `main.rs` | 二进制入口，解析 CLI 参数并调用 `run_main()` |
| `app.rs` | `App::run()` 聊天会话主循环 |
| `cli.rs` | `Cli` 命令行参数结构体 |
| `app_event.rs` | `AppEvent` 枚举，所有内部事件类型 |
| `chatwidget.rs` | `ChatWidget` 聊天组件模块入口 |
| `tui.rs` | `Tui` 终端管理、`init()`/`restore()` |
| `markdown_render.rs` | `render_markdown_text()` 公开 API |
