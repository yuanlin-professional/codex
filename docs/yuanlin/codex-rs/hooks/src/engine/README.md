# hooks/src/engine — 钩子引擎

## 功能概述
hooks 系统的执行引擎，负责发现、调度和运行钩子命令。

## 目录结构
| 文件 | 说明 |
|------|------|
| `command_runner.rs` | 命令运行器 |
| `config.rs` | 引擎配置 |
| `discovery.rs` | 钩子发现 |
| `dispatcher.rs` | 事件调度器 |
| `mod.rs` | 模块入口 |
| `output_parser.rs` | 输出解析器 |
| `schema_loader.rs` | Schema 加载器 |
