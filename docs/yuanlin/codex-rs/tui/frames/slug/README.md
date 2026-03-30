# frames/slug/

## 功能概述

`slug/` 目录包含蛞蝓（slug）风格的 ASCII 动画帧资源。该动画使用特定字符模式构成缓慢移动的视觉效果，用于 TUI 的加载动画和 onboarding 欢迎界面。

## 目录结构

```
slug/
├── frame_1.txt
├── frame_2.txt
├── ...
└── frame_36.txt
```

共 36 帧文本文件，通过 `include_str!` 宏在编译时嵌入 `FRAMES_SLUG` 常量。

## 核心接口/API

无独立 API。帧内容通过 `src/frames.rs` 中的 `FRAMES_SLUG: [&str; 36]` 常量访问。
