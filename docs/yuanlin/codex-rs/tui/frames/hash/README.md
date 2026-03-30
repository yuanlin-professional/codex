# frames/hash/

## 功能概述

`hash/` 目录包含井号（hash）风格的 ASCII 动画帧资源。该动画使用 `#` 字符构成视觉效果，用于 TUI 的加载动画和 onboarding 欢迎界面。

## 目录结构

```
hash/
├── frame_1.txt
├── frame_2.txt
├── ...
└── frame_36.txt
```

共 36 帧文本文件，通过 `include_str!` 宏在编译时嵌入 `FRAMES_HASH` 常量。

## 核心接口/API

无独立 API。帧内容通过 `src/frames.rs` 中的 `FRAMES_HASH: [&str; 36]` 常量访问。
