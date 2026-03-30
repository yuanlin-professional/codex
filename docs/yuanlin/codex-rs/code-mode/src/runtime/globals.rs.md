# `globals.rs` -- V8 全局对象安装

## 功能概述

本模块负责在 V8 隔离上下文中安装 Code Mode 所需的全局对象和辅助函数。它移除默认的 `console` 对象，创建 `tools` 对象（包含所有启用的嵌套工具方法），并注册 `text`、`image`、`store`、`load`、`notify`、`yield_control`、`exit` 等全局辅助函数以及 `ALL_TOOLS` 元数据数组。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `install_globals` | `pub fn install_globals(scope: &mut v8::PinScope) -> Result<(), String>` | 主入口：安装所有全局对象和函数到 V8 上下文 |
| `build_tools_object` | `fn build_tools_object(scope) -> Result<v8::Object, String>` | 构建 `tools` 对象，每个工具方法绑定到对应的 callback |
| `build_all_tools_value` | `fn build_all_tools_value(scope) -> Result<v8::Value, String>` | 构建 `ALL_TOOLS` 数组，包含 `{ name, description }` 条目 |
| `helper_function` | `fn helper_function(scope, name, callback) -> Result<v8::Function, String>` | 创建带名称元数据的辅助函数模板 |
| `tool_function` | `fn tool_function(scope, tool_name) -> Result<v8::Function, String>` | 创建工具调用函数，tool_name 作为 callback 数据 |
| `set_global` | `fn set_global(scope, global, name, value) -> Result<(), String>` | 在全局对象上设置属性 |

## 依赖关系

- **引用的 crate**: `v8`
- **被引用方**: `runtime/mod.rs`

## 实现备注

- `console` 被移除以防止沙箱化的 JavaScript 代码通过 console.log 输出
- 工具方法通过 `v8::FunctionTemplate::builder` 绑定回调，工具名称通过 `data` 参数传递
- `ALL_TOOLS` 为只读元数据，用于让 JavaScript 代码发现可用工具
