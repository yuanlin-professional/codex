# code-mode

## 功能概述

`codex-code-mode` 是 Codex 项目的代码模式（Code Mode）执行引擎，基于 V8 JavaScript 引擎提供一个安全的代码执行环境。它允许 AI 代理在对话过程中编写和执行 JavaScript 代码，支持 exec（执行代码）和 wait（等待结果）两种操作模式，实现了代码执行的双工交互流程。

## 架构说明

该模块由以下核心组件组成：

### 1. service (CodeModeService)
代码模式服务层，管理 V8 运行时的生命周期和任务调度：
- **CodeModeTurnHost** - 宿主端接口，用于提交执行请求
- **CodeModeTurnWorker** - 工作端接口，在 V8 运行时中执行代码

### 2. runtime
V8 运行时封装，处理代码的实际执行：
- **ExecuteRequest** - 代码执行请求
- **WaitRequest** - 等待结果请求
- **RuntimeResponse** - 运行时响应

执行参数：
- `DEFAULT_EXEC_YIELD_TIME_MS` - exec 调用默认超时
- `DEFAULT_WAIT_YIELD_TIME_MS` - wait 调用默认超时
- `DEFAULT_MAX_OUTPUT_TOKENS_PER_EXEC_CALL` - 单次 exec 调用最大输出 token 数

### 3. description
工具描述生成和增强：
- `build_exec_tool_description` / `build_wait_tool_description` - 构建工具描述文本
- `augment_tool_definition` - 增强工具定义以支持代码模式
- `render_json_schema_to_typescript` - 将 JSON Schema 渲染为 TypeScript 类型定义
- `CODE_MODE_PRAGMA_PREFIX` - 代码模式标识前缀

### 4. response
响应处理：
- `FunctionCallOutputContentItem` - 函数调用输出内容项
- `ImageDetail` - 图片详情

## 目录结构

```
code-mode/
  Cargo.toml
  src/
    lib.rs           # 模块入口，导出公共 API 和常量
    description.rs   # 工具描述构建和 JSON Schema 到 TypeScript 转换
    response.rs      # 响应类型定义
    runtime.rs       # V8 运行时封装和执行引擎
    service.rs       # 代码模式服务（Host/Worker 双端）
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `v8` | V8 JavaScript 引擎（代码执行沙箱） |
| `async-trait` | 异步 trait 支持 |
| `serde` / `serde_json` | 请求/响应序列化 |
| `tokio` / `tokio-util` | 异步运行时和同步原语 |
| `tracing` | 日志追踪 |

## 核心接口/API

### 服务层

- **`CodeModeService`** - 代码模式服务，管理 V8 运行时
- **`CodeModeTurnHost`** - 宿主端，提交执行/等待请求
- **`CodeModeTurnWorker`** - 工作端，在 V8 中执行代码

### 工具描述

```rust
pub fn build_exec_tool_description() -> String;       // 构建 exec 工具描述
pub fn build_wait_tool_description() -> String;        // 构建 wait 工具描述
pub fn augment_tool_definition(def: ToolDefinition);   // 增强工具定义
pub fn render_json_schema_to_typescript(schema: &Value) -> String;  // Schema -> TypeScript
pub fn is_code_mode_nested_tool(name: &str) -> bool;   // 判断是否为代码模式嵌套工具
pub fn normalize_code_mode_identifier(id: &str) -> String;  // 规范化标识符
pub fn parse_exec_source(input: &str) -> String;       // 解析 exec 源码
```

### 运行时

- **`ExecuteRequest`** - 代码执行请求
- **`WaitRequest`** - 等待结果请求
- **`RuntimeResponse`** - 运行时响应

### 常量

- `PUBLIC_TOOL_NAME` = `"exec"` - 公共工具名称
- `WAIT_TOOL_NAME` = `"wait"` - 等待工具名称
- `CODE_MODE_PRAGMA_PREFIX` - 代码模式标识前缀
