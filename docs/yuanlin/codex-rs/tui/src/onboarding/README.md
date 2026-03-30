# src/onboarding/

## 功能概述

`onboarding/` 模块实现 TUI 的用户引导流程。在用户首次使用 Codex 或进入新的项目目录时，会显示引导界面，包含以下步骤：

1. **欢迎界面**（`welcome.rs`）：显示 ASCII 动画和欢迎信息
2. **认证登录**（`auth.rs` 及 `auth/` 子目录）：支持 ChatGPT 认证和 API Key 认证
3. **目录信任决策**（`trust_directory.rs`）：询问用户是否信任当前工作目录

引导流程由 `OnboardingScreen` 状态机驱动，按步骤依次显示每个界面。

## 目录结构

```
onboarding/
├── mod.rs                  # 模块入口，重导出核心类型
├── onboarding_screen.rs    # 引导流程主屏幕状态机和步骤编排
├── auth.rs                 # 认证/登录 UI 组件
├── auth/                   # 认证子模块
│   └── headless_chatgpt_login.rs  # 无头 ChatGPT 登录实现
├── trust_directory.rs      # 目录信任决策 UI
├── welcome.rs              # 欢迎界面 UI（含 ASCII 动画）
└── snapshots/              # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `run_onboarding_app()` | 运行引导流程的主函数 |
| `OnboardingScreen` | 引导流程主屏幕，管理步骤切换 |
| `OnboardingScreenArgs` | 引导屏幕参数（是否显示登录/信任屏幕等） |
| `Step` | 步骤枚举（Welcome/Auth/TrustDirectory） |
| `KeyboardHandler` | 键盘事件处理 trait |
| `StepState` | 步骤状态枚举（Hidden/InProgress/Complete） |
| `StepStateProvider` | 步骤状态查询 trait |
| `AuthModeWidget` | 认证模式选择组件 |
| `SignInState` / `SignInOption` | 登录状态和登录选项 |
| `TrustDirectoryWidget` | 目录信任决策组件 |
| `TrustDirectorySelection` | 信任决策枚举（Trust/Quit） |
| `WelcomeWidget` | 欢迎界面组件（含 ASCII 动画播放） |
| `mark_url_hyperlink()` | 在文本中标记超链接的辅助函数 |
