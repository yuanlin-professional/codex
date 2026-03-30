# `config.rs` -- Hook 配置文件（hooks.json）的反序列化结构

## 功能概述

本文件定义了 `hooks.json` 配置文件的 Rust 反序列化类型。配置文件按事件类型（PreToolUse、PostToolUse、SessionStart、UserPromptSubmit、Stop）组织 Hook 处理器，每个事件下可包含多个 matcher 分组，每组内包含多个处理器定义。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HooksFile` | struct | 顶层配置结构，包含一个 `hooks` 字段 |
| `HookEvents` | struct | 按事件类型分组的 Hook 列表，字段名使用 serde rename 映射到 JSON 中的 PascalCase |
| `MatcherGroup` | struct | 一个 matcher 分组，包含可选的 `matcher` 模式和一组 `hooks` 处理器 |
| `HookHandlerConfig` | enum (tagged) | Hook 处理器配置，通过 `type` 字段区分三种类型：`command`（命令）、`prompt`（提示）和 `agent`（代理） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| （无公开函数） | -- | 本文件仅定义数据结构，由 `discovery` 模块使用 |

## 依赖关系

- **引用的 crate**: `serde`（Deserialize）
- **被引用方**: `engine::discovery`（读取并解析 hooks.json）

## 实现备注

- `HookHandlerConfig::Command` 包含 `command`（命令字符串）、`timeout_sec`（超时，支持 `timeout` 和 `timeoutSec` 两个 JSON 字段名）、`async`（是否异步，当前不支持）和 `status_message`。
- `prompt` 和 `agent` 类型当前为空结构体，发现时会在 `discovery` 中生成警告跳过。
