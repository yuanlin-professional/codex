# `voice.rs` — 语音输入/输出和实时音频处理

## 功能概述

实现 TUI 的实时语音模式功能，包括麦克风音频捕获（`VoiceCapture`）、
音频回放（`RealtimeAudioPlayer`）和录音电平表（`RecordingMeterState`）。
支持 f32/i16/u16 多种音频格式，自动将捕获音频转换为模型要求的 24kHz 单声道格式，
并通过 base64 编码发送。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `VoiceCapture` | struct | 麦克风捕获，持有 cpal Stream |
| `RealtimeAudioPlayer` | struct | 音频回放，持有输出 Stream 和采样队列 |
| `RecordingMeterState` | struct | 录音电平表状态（EMA 噪声估计+包络跟踪） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `VoiceCapture::start_realtime` | `(config, tx) -> Result<Self>` | 启动实时麦克风捕获 |
| `RealtimeAudioPlayer::start` | `(config) -> Result<Self>` | 启动音频回放 |
| `RealtimeAudioPlayer::enqueue_frame` | `(&self, frame) -> Result<()>` | 将音频帧加入播放队列 |
| `RecordingMeterState::next_text` | `(&mut self, peak) -> String` | 生成 4 字符 Braille 电平表 |
| `convert_pcm16` | `(input, in_rate, in_ch, out_rate, out_ch) -> Vec<i16>` | PCM 重采样和通道转换 |

## 依赖关系

- **引用的 crate**: `cpal`, `base64`, `codex_core`, `codex_protocol`
- **引用的模块**: `crate::audio_device`, `crate::app_event_sender`

## 实现备注

- 电平表使用 EMA 噪声估计和攻击/释放包络跟踪，通过对数压缩映射到 7 级 Braille 符号。
- 重采样使用最近邻插值，适用于语音质量要求。
- 通道转换支持单声道到多声道（复制）、多声道到单声道（取平均）。
