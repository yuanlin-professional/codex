# `live_cli.rs` — 测试模块

## 测试覆盖范围
实时 CLI 冒烟测试（默认标记为 `#[ignore]`），需要设置 `OPENAI_API_KEY` 环境变量才能运行，验证 codex-rs 二进制文件与真实 OpenAI API 端点的交互。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `live_create_file_hello_txt` | 通过 shell 工具使用 apply_patch 命令创建 hello.txt 文件并验证内容 |
| `live_print_working_directory` | 通过 shell 函数打印当前工作目录并验证输出包含正确路径 |
