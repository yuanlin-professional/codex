# `models.py` -- 核心数据模型与类型定义

## 功能概述

本模块定义了 SDK 内部使用的核心数据类型，包括 JSON 值类型别名、通知载荷联合类型、通知容器、未知通知回退类型，以及 `InitializeResponse` 和 `ServerInfo` 等初始化阶段使用的 Pydantic 模型。`NotificationPayload` 联合类型汇总了所有已知的服务端通知类型和一个 `UnknownNotification` 回退类型。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `JsonScalar` | 类型别名 | JSON 标量类型（str/int/float/bool/None） |
| `JsonValue` | 类型别名 | 递归 JSON 值类型 |
| `JsonObject` | 类型别名 | JSON 对象类型（`dict[str, JsonValue]`） |
| `UnknownNotification` | 数据类 | 未知通知的回退容器，保留原始参数字典 |
| `NotificationPayload` | 类型别名 | 所有已知通知类型与 `UnknownNotification` 的联合类型 |
| `Notification` | 数据类 | 通知容器，包含方法名和已解析的载荷 |
| `ServerInfo` | Pydantic 模型 | 服务器信息，包含名称和版本 |
| `InitializeResponse` | Pydantic 模型 | initialize 握手响应，包含服务器信息、user-agent 和平台信息 |

## 依赖关系

- **引用的模块**: `.generated.v2_all`（所有通知类型类）, `pydantic`
- **被引用方**: `.client`, `.async_client`, `.api`, `._run`, `._inputs`

## 实现备注

- `NotificationPayload` 联合类型包含约 30 种已知通知类型，覆盖了 app-server 协议 v2 的完整通知集合
- `UnknownNotification` 作为回退类型，确保未识别的通知不会导致解析失败
