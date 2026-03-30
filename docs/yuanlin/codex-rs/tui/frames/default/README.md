# frames/default/

## 功能概述

`default/` 目录包含默认风格的 ASCII 动画帧资源。这是 TUI 加载动画和 onboarding 欢迎界面的默认动画方案。

## 目录结构

```
default/
├── frame_1.txt
├── frame_2.txt
├── ...
└── frame_36.txt
```

共 36 帧文本文件，通过 `include_str!` 宏在编译时嵌入 `FRAMES_DEFAULT` 常量。

## 核心接口/API

无独立 API。帧内容通过 `src/frames.rs` 中的 `FRAMES_DEFAULT: [&str; 36]` 常量访问。
