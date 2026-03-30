# `v2_all.py` -- app-server 协议 v2 的全部自动生成类型

## 功能概述

本文件由 `datamodel-code-generator` 工具从 `codex_app_server_protocol.v2.schemas.json` JSON Schema 自动生成，包含了 app-server 协议 v2 的全部 Pydantic 数据模型。文件体量庞大，定义了数百个类，涵盖请求参数、响应结构、通知载荷、枚举类型、线程/轮次/item 等所有协议实体。SDK 的几乎所有模块都依赖此文件中的类型。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadStartParams` | Pydantic 模型 | 创建线程的请求参数 |
| `ThreadResumeParams` | Pydantic 模型 | 恢复线程的请求参数 |
| `ThreadListParams` | Pydantic 模型 | 列出线程的请求参数 |
| `ThreadForkParams` | Pydantic 模型 | 分叉线程的请求参数 |
| `TurnStartParams` | Pydantic 模型 | 启动轮次的请求参数 |
| `TurnStatus` | 枚举 | 轮次状态（completed、failed 等） |
| `TurnCompletedNotification` | Pydantic 模型 | 轮次完成通知 |
| `AgentMessageDeltaNotification` | Pydantic 模型 | 代理消息增量通知（流式文本） |
| `ItemCompletedNotification` | Pydantic 模型 | Item 完成通知 |
| `ThreadItem` | Pydantic 模型 | 线程中的一个 item（联合类型） |
| `AskForApproval` | Pydantic 模型 | 审批策略配置 |
| `SandboxPolicy` | Pydantic 模型 | 沙箱策略配置 |
| `ReasoningEffort` | 枚举 | 推理努力级别 |
| `Personality` | 枚举 | 人格类型 |
| `ModelListResponse` | Pydantic 模型 | 模型列表响应 |

## 依赖关系

- **引用的模块**: `pydantic`（BaseModel、Field、RootModel 等）
- **被引用方**: SDK 中几乎所有模块：`.__init__`, `.api`, `.client`, `.async_client`, `.models`, `._run`, `.generated.notification_registry`

## 实现备注

- 所有模型使用 `populate_by_name=True` 配置，支持 snake_case 和 camelCase 双向访问
- 字段使用 `Field(alias="camelCaseName")` 映射 JSON 中的驼峰命名到 Python 的蛇形命名
- 使用 `--use-title-as-name` 生成选项确保联合类型分支获得稳定、有意义的类名
- 使用 `--enum-field-as-literal one` 使单值枚举字段生成为 `Literal` 类型
- 由 `ruff-format` 格式化以保持确定性输出
