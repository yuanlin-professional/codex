# `prompts.rs` -- 记忆系统提示词模板构建

## 功能概述

负责构建记忆系统各阶段所需的提示词。使用嵌入式 Markdown 模板（通过 `include_str!` 加载），结合运行时参数进行渲染。主要提供三个模板的渲染函数：Phase 2 合并提示词、Phase 1 stage-one 输入消息、以及读路径下的记忆工具开发者指令。对于大型 rollout 内容，会根据模型上下文窗口大小进行智能截断。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CONSOLIDATION_PROMPT_TEMPLATE` | LazyLock\<Template\> | Phase 2 合并提示词模板（惰性初始化） |
| `STAGE_ONE_INPUT_TEMPLATE` | LazyLock\<Template\> | Phase 1 输入消息模板 |
| `MEMORY_TOOL_DEVELOPER_INSTRUCTIONS_TEMPLATE` | LazyLock\<Template\> | 记忆工具开发者指令模板 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_consolidation_prompt` | `fn(&Path, &Phase2InputSelection) -> String` | 构建 Phase 2 合并子代理的提示词，包含选中/移除的输入记忆清单 |
| `build_stage_one_input_message` | `fn(&ModelInfo, &Path, &Path, &str) -> Result<String>` | 构建 Phase 1 用户消息，按模型上下文窗口截断 rollout 内容 |
| `build_memory_tool_developer_instructions` | `async fn(&Path) -> Option<String>` | 读取 memory_summary.md 并渲染为开发者指令，截断大文件 |
| `render_phase2_input_selection` | `fn(&Phase2InputSelection) -> String` | 渲染 Phase 2 输入选择的详细信息（新增/保留/移除的记忆列表） |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_state`、`codex_utils_output_truncation`、`codex_utils_template`、`tokio::fs`
- **被引用方**: `phase1.rs` 调用 `build_stage_one_input_message`；`phase2.rs` 调用 `build_consolidation_prompt`；外部模块调用 `build_memory_tool_developer_instructions`

## 实现备注

- rollout 内容截断策略为取模型上下文窗口的 `effective_context_window_percent`（通常 95%）再取其 70%，若模型未提供上下文窗口信息则回退到默认 150,000 token。
- `memory_summary.md` 截断限制为 5,000 token。
- Phase 2 提示词中区分了 "added"（新增）和 "retained"（保留）的输入记忆，帮助合并代理理解增量变化。
- 模板渲染失败时使用硬编码的回退文本，保证功能不中断。
