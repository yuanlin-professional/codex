# sdk/python -- Codex App Server Python SDK

## 功能概述

`codex-app-server-sdk` 是 Codex app-server JSON-RPC v2 协议的实验性 Python SDK。它通过 stdio 与 `codex` CLI 进程通信，提供同步和异步两种客户端接口，方便开发者在 Python 脚本和应用中嵌入 Codex 代理能力。

SDK 的线模型层（wire-model）由内置的 v2 schema 自动生成，以 Pydantic 模型暴露，使用 snake_case Python 字段名，序列化时自动转换为 app-server 的 camelCase 格式。

## 目录结构

```
sdk/python/
├── pyproject.toml                  # 项目构建配置（hatchling）
├── README.md                       # 英文说明文档
├── _runtime_setup.py               # 运行时初始化脚本
├── src/
│   └── codex_app_server/
│       ├── __init__.py             # 包入口，导出 Codex 类
│       ├── api.py                  # 核心 API 封装
│       ├── client.py               # 同步客户端实现
│       ├── async_client.py         # 异步客户端实现
│       ├── models.py               # 数据模型定义
│       ├── errors.py               # 错误类型定义
│       ├── retry.py                # 重试策略（retry_on_overload）
│       ├── _inputs.py              # 输入处理
│       ├── _run.py                 # 运行逻辑
│       ├── py.typed                # PEP 561 类型标记
│       └── generated/              # 自动生成的协议模型
│           ├── __init__.py
│           ├── notification_registry.py
│           └── v2_all.py
├── examples/                       # 示例代码（14 个递进场景）
│   ├── _bootstrap.py
│   ├── 01_quickstart_constructor/  # 快速入门（同步/异步）
│   ├── 02_turn_run/                # Turn 运行
│   ├── 03_turn_stream_events/      # 流式事件
│   ├── 04_models_and_metadata/     # 模型与元数据
│   ├── 05_existing_thread/         # 恢复已有 Thread
│   ├── 06_thread_lifecycle_and_controls/  # Thread 生命周期控制
│   ├── 07_image_and_text/          # 图片+文本输入
│   ├── 08_local_image_and_text/    # 本地图片+文本
│   ├── 09_async_parity/            # 异步对等测试
│   ├── 10_error_handling_and_retry/ # 错误处理与重试
│   ├── 11_cli_mini_app/            # CLI 迷你应用
│   ├── 12_turn_params_kitchen_sink/ # Turn 参数全览
│   ├── 13_model_select_and_turn_params/ # 模型选择与 Turn 参数
│   └── 14_turn_controls/           # Turn 控制
├── notebooks/                      # Jupyter 教程 notebook
├── docs/                           # 文档（入门、API 参考、FAQ）
├── scripts/                        # 维护脚本（类型生成、发布暂存）
└── tests/                          # 测试用例
```

## 依赖关系

- **构建系统**：hatchling >= 1.24.0
- **运行时依赖**：pydantic >= 2.12
- **开发依赖**：pytest >= 8.0、datamodel-code-generator == 0.31.2、ruff >= 0.11
- **Python 版本**：>= 3.10（支持 3.10 ~ 3.13）
- **运行时包**：发布版本需配合 `codex-cli-bin` 平台运行时包；本地开发可通过 `AppServerConfig(codex_bin=...)` 指定二进制路径
- **协议版本**：Codex app-server JSON-RPC v2

## 核心接口/API

| 接口 | 说明 |
|------|------|
| `Codex()` | 主入口，构造时立即执行 startup + initialize；建议使用 `with` 上下文管理器 |
| `codex.thread_start(model=...)` | 创建新会话线程 |
| `thread.run("...")` | 执行一个 turn 并等待完成，返回 `result.final_response` 和 `result.items` |
| `thread.turn(...)` | 高级接口，支持流式、转向控制、中断等 |
| `codex_app_server.retry.retry_on_overload` | 过载重试装饰器 |
| `AsyncCodex` | 异步版本客户端，API 与同步版本对等 |
