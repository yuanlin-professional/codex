# `_bootstrap.py` -- 示例运行的公共引导模块

## 功能概述

本模块为 `examples/` 目录下的所有示例脚本提供公共引导功能。它负责将 SDK 源码路径添加到 `sys.path` 以支持本地开发模式运行、确保运行时依赖（pydantic）已安装、自动下载并安装锁定版本的 Codex 运行时二进制，以及提供示例中常用的辅助函数。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `ensure_local_sdk_src()` | 函数 | 将 `sdk/python/src` 添加到 sys.path，使示例无需安装即可导入 SDK |
| `runtime_config()` | 函数 | 返回适用于本地开发的 `AppServerConfig`，自动确保运行时已安装 |
| `temporary_sample_image_path()` | 上下文管理器 | 生成一个临时的 96x96 PNG 示例图片文件，用于图片输入示例 |
| `server_label()` | 函数 | 从 metadata 对象中提取服务器标签字符串（名称 + 版本） |
| `find_turn_by_id()` | 函数 | 在 turns 列表中按 ID 查找特定的 turn 对象 |
| `assistant_text_from_turn()` | 函数 | 从 turn 对象的 items 中提取助手消息文本 |

## 依赖关系

- **引用的模块**: `_runtime_setup`（`ensure_runtime_package_installed`）, `codex_app_server`（`AppServerConfig`）
- **被引用方**: 所有 `examples/` 子目录中的示例脚本

## 实现备注

- `_generated_sample_png_bytes()` 纯 Python 生成一个 4 色块的 96x96 PNG 图片，无需外部图像库
- `assistant_text_from_turn()` 同时支持 `agentMessage` 和旧版 `message` 两种 item 格式
