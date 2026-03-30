# `run.test.ts` -- 测试模块

## 测试覆盖范围

测试 `Codex` SDK 的核心 `run()` 方法，覆盖基本对话、多轮对话、线程恢复、各类线程选项传递（模型、沙箱模式、推理能力、网络访问、Web 搜索、审批策略、额外目录）、配置覆盖优先级、结构化输出 schema、结构化输入、图片转发、工作目录以及错误处理等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `returns thread events` | 基本对话：验证返回的 items、usage 和 thread.id |
| `sends previous items when run is called twice` | 多轮对话：第二次请求应包含第一轮的助手响应 |
| `continues the thread when run is called twice with options` | 多轮对话续接：验证第二轮输入和历史上下文 |
| `resumes thread by id` | 通过 `resumeThread(id)` 恢复对话，验证 thread ID 一致性 |
| `passes turn options to exec` | 验证 model 和 sandboxMode 正确传递为 CLI 参数 |
| `passes modelReasoningEffort to exec` | 验证 `--config model_reasoning_effort="high"` 参数 |
| `passes networkAccessEnabled to exec` | 验证 `--config sandbox_workspace_write.network_access=true` 参数 |
| `passes webSearchEnabled to exec` | 验证 `webSearchEnabled: true` 转换为 `--config web_search="live"` |
| `passes webSearchMode to exec` | 验证 `webSearchMode: "cached"` 转换为 `--config web_search="cached"` |
| `passes webSearchEnabled false to exec` | 验证 `webSearchEnabled: false` 转换为 `--config web_search="disabled"` |
| `passes approvalPolicy to exec` | 验证 `--config approval_policy="on-request"` 参数 |
| `passes CodexOptions config overrides as TOML --config flags` | 验证嵌套配置对象的 TOML 序列化 |
| `lets thread options override CodexOptions config overrides` | 验证 ThreadOptions 的 approvalPolicy 覆盖 CodexOptions 中的同名配置 |
| `passes additionalDirectories as repeated flags` | 验证多个 `--add-dir` 参数正确传递 |
| `writes output schema to a temporary file and forwards it` | 验证 outputSchema 写入临时文件并通过 `--output-schema` 传递，完成后文件已清理 |
| `combines structured text input segments` | 验证多段文本输入合并为单个提示词 |
| `forwards images to exec` | 验证图片路径通过 `--image` 参数传递 |
| `runs in provided working directory` | 验证 `--cd` 参数传递 |
| `throws if working directory is not git and no skipGitRepoCheck` | 非 git 目录且未设置 skipGitRepoCheck 时应抛出错误 |
| `sets the codex sdk originator header` | 验证请求头包含 `originator: codex_sdk_ts` |
| `throws ThreadRunError on turn failures` | API 返回错误时应抛出包含错误消息的异常 |
