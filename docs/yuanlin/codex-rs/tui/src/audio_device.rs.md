# `audio_device.rs` — 实时音频设备选择与配置

## 功能概述

本文件负责管理实时语音输入/输出的音频设备选择和流配置。它使用 `cpal` crate 枚举系统可用的音频设备，根据用户配置选择最合适的输入/输出设备，并为麦克风输入计算最优采样率配置（偏好 24kHz 单声道 I16 格式）。当配置的设备不可用时，会自动回退到系统默认设备并发出警告。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `list_realtime_audio_device_names` | `fn(kind) -> Result<Vec<String>, String>` | 列出指定类型（麦克风/扬声器）的可用设备名称 |
| `select_configured_input_device_and_config` | `fn(config) -> Result<(Device, SupportedStreamConfig), String>` | 根据配置选择麦克风设备和流配置 |
| `select_configured_output_device_and_config` | `fn(config) -> Result<(Device, SupportedStreamConfig), String>` | 根据配置选择扬声器设备和流配置 |
| `preferred_input_config` | `fn(device) -> Result<SupportedStreamConfig, String>` | 为输入设备计算最优流配置 |

## 依赖关系

- **引用的 crate**: `cpal`（跨平台音频 I/O）、`codex_core::config::Config`
- **被引用方**: `voice` 模块中的 `VoiceCapture` 和 `RealtimeAudioPlayer`

## 实现备注

- 输入配置偏好评分通过 `(采样率差异, 声道差异, 格式排名)` 元组的最小值选择实现。
- 仅在启用 `voice-input` feature 且非 Linux 平台时编译完整实现；其他情况提供返回错误的桩实现。
- 设备查找失败时生成包含设备名称和设备类型的详细错误消息。
