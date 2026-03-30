# `notification_registry.py` -- 通知方法名到 Pydantic 模型的映射注册表

## 功能概述

本文件由 `scripts/update_sdk_artifacts.py` 自动生成，不应手动编辑。它定义了一个字典 `NOTIFICATION_MODELS`，将 JSON-RPC 通知方法名（如 `"turn/completed"`、`"item/agentMessage/delta"`）映射到对应的 Pydantic 模型类。该注册表被 `AppServerClient._coerce_notification()` 用于将原始 JSON 通知载荷反序列化为强类型的 Python 对象。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `NOTIFICATION_MODELS` | 字典 | 方法名 -> Pydantic 模型类的映射，约 50 个条目 |

## 依赖关系

- **引用的模块**: `.v2_all`（导入所有通知模型类）
- **被引用方**: `..client`（`AppServerClient._coerce_notification()` 使用）

## 实现备注

- 映射涵盖了账户、线程、轮次、item、MCP 服务器、文件系统等全部通知类别
- 方法名使用 app-server 协议 v2 的规范格式（如 `"thread/tokenUsage/updated"`）
- 文件头部标注 "DO NOT EDIT MANUALLY"，任何修改应通过 `generate-types` 命令重新生成
