# `update_sdk_artifacts.py` -- SDK 构建与代码生成统一入口脚本

## 功能概述

本脚本是 Python SDK 的唯一构建维护入口，提供三个子命令：`generate-types`（从协议 schema 重新生成 Python 类型）、`stage-sdk`（构建可发布的 SDK 包）和 `stage-runtime`（构建平台特定的运行时包）。类型生成流程包括：规范化 JSON Schema（展平字符串枚举 oneOf、为联合分支添加稳定标题）、调用 `datamodel-code-generator` 生成 Pydantic 模型、生成通知注册表、以及更新公共 API 层的生成代码块。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `generate_types()` | 函数 | 类型生成主入口，依次调用 v2_all、notification_registry 和 public_api_flat_methods 生成 |
| `generate_v2_all()` | 函数 | 从 JSON Schema 生成 `v2_all.py`，包含 schema 规范化和 codegen 调用 |
| `generate_notification_registry()` | 函数 | 从 `ServerNotification.json` 生成通知方法到模型的映射 |
| `generate_public_api_flat_methods()` | 函数 | 反射 Pydantic 模型字段，生成 `api.py` 中的展平方法签名 |
| `stage_python_sdk_package()` | 函数 | 将 SDK 源码复制到暂存目录并重写版本号和运行时依赖 |
| `stage_python_runtime_package()` | 函数 | 将运行时二进制复制到暂存目录并设置可执行权限 |
| `_annotate_schema()` | 内部函数 | 递归遍历 schema，为 oneOf/anyOf 分支添加稳定的标题名 |
| `_flatten_string_enum_one_of()` | 内部函数 | 将单值字符串枚举的 oneOf 分支展平为普通枚举 |
| `_load_public_fields()` | 内部函数 | 反射加载 Pydantic 模型字段，提取字段名、类型注解和是否必需 |
| `_replace_generated_block()` | 内部函数 | 替换源文件中 BEGIN/END GENERATED 标记之间的代码块 |
| `PublicFieldSpec` | 数据类 | 描述公共 API 方法的单个参数 |
| `CliOps` | 数据类 | CLI 操作的可注入接口（便于测试） |
| `main()` | 函数 | CLI 入口，解析参数并执行对应子命令 |

## 依赖关系

- **引用的模块**: `argparse`, `importlib`, `json`, `re`, `shutil`, `subprocess`, `sys`, `tempfile`, `typing`, `pathlib`
- **被引用方**: `_runtime_setup.py`（动态加载本脚本的 `stage_python_runtime_package`）, 测试文件, CI 流水线

## 实现备注

- schema 规范化是关键步骤：确保 `datamodel-code-generator` 为联合分支生成稳定、可读的类名（而非 `Model1`、`Model2`）
- 标题生成算法基于分支的鉴别器字段（type、method、mode 等）和基础名称组合
- `_annotation_to_source()` 使用 `typing.get_origin`/`get_args` 将运行时类型注解转换回源码字符串
- `FIELD_ANNOTATION_OVERRIDES` 允许对特定字段（如 `config`、`output_schema`）覆盖自动推断的注解类型
