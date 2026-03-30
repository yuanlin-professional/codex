# frames/

## 功能概述

`frames/` 目录存放 TUI 中 ASCII 动画的帧资源文件。这些动画用于 onboarding 欢迎界面和加载指示器。每种动画风格对应一个子目录，每个子目录包含 36 帧文本文件（`frame_1.txt` 到 `frame_36.txt`）。

动画帧在编译时通过 `include_str!` 宏嵌入到二进制文件中（参见 `src/frames.rs`），默认帧切换间隔为 80ms。

## 目录结构

```
frames/
├── default/    # 默认动画风格
├── codex/      # Codex 品牌动画
├── openai/     # OpenAI 品牌动画
├── blocks/     # 方块风格动画
├── dots/       # 点阵风格动画
├── hash/       # 井号风格动画
├── hbars/      # 水平条纹动画
├── vbars/      # 垂直条纹动画
├── shapes/     # 几何形状动画
└── slug/       # 蛞蝓风格动画
```

每个子目录包含：
- `frame_1.txt` ~ `frame_36.txt`：36 帧 ASCII 艺术文本文件

## 核心接口/API

动画帧通过 `src/frames.rs` 中的常量暴露：

| 常量 | 说明 |
|------|------|
| `FRAMES_DEFAULT` | 默认动画的 36 帧 |
| `FRAMES_CODEX` | Codex 品牌动画的 36 帧 |
| `FRAMES_OPENAI` | OpenAI 品牌动画的 36 帧 |
| `FRAMES_BLOCKS` | 方块风格动画的 36 帧 |
| `FRAMES_DOTS` | 点阵风格动画的 36 帧 |
| `FRAMES_HASH` | 井号风格动画的 36 帧 |
| `FRAMES_HBARS` | 水平条纹动画的 36 帧 |
| `FRAMES_VBARS` | 垂直条纹动画的 36 帧 |
| `FRAMES_SHAPES` | 几何形状动画的 36 帧 |
| `FRAMES_SLUG` | 蛞蝓风格动画的 36 帧 |
| `ALL_VARIANTS` | 所有动画变体的集合引用 |
| `FRAME_TICK_DEFAULT` | 默认帧切换间隔（80ms） |
