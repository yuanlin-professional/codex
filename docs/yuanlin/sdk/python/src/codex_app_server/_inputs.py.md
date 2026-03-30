# `_inputs.py` -- 用户输入类型定义与序列化

## 功能概述

本模块定义了 SDK 的输入类型体系，包括文本、图片、本地图片、技能和提及五种输入数据类。同时提供了将这些高级输入类型转换为 JSON-RPC 传输格式（wire format）的序列化函数，以及将字符串快捷输入规范化为 `TextInput` 的辅助函数。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `TextInput` | 数据类 | 文本输入，包含 `text` 字段 |
| `ImageInput` | 数据类 | 远程图片输入，包含 `url` 字段 |
| `LocalImageInput` | 数据类 | 本地图片输入，包含 `path` 字段 |
| `SkillInput` | 数据类 | 技能输入，包含 `name` 和 `path` 字段 |
| `MentionInput` | 数据类 | 提及输入，包含 `name` 和 `path` 字段 |
| `InputItem` | 类型别名 | 五种输入类型的联合类型 |
| `Input` | 类型别名 | 单个 `InputItem` 或其列表 |
| `RunInput` | 类型别名 | `Input` 或纯字符串（便捷入口） |
| `_to_wire_item()` | 内部函数 | 将单个 `InputItem` 转为 JSON 字典 |
| `_to_wire_input()` | 内部函数 | 将 `Input` 转为 JSON 字典列表 |
| `_normalize_run_input()` | 内部函数 | 将 `RunInput`（可能是字符串）规范化为 `Input` |

## 依赖关系

- **引用的模块**: `.models`（`JsonObject` 类型）
- **被引用方**: `.api`（`Codex`、`Thread` 等高级 API 使用）

## 实现备注

- 所有数据类使用 `slots=True` 以优化内存占用和属性访问速度
- wire format 中的 `type` 字段使用 camelCase 命名（如 `localImage`），与 app-server 协议保持一致
