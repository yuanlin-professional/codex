# template

## 功能概述

`codex-utils-template` 实现了一个最小化但严格的模板引擎，用于 Codex 的提示词（prompt）和文本资源的变量插值。

支持的语法：
- `{{ name }}` -- 占位符插值（名称两侧的空白被忽略）
- `{{{{` -- 转义为字面量 `{{`
- `}}}}` -- 转义为字面量 `}}`

设计原则：严格模式，不允许缺失变量、多余变量或重复变量，确保模板渲染结果的可预测性。

## 目录结构

```
template/
├── Cargo.toml
└── src/
    └── lib.rs          # Template 类型与 render() 函数
```

## 依赖关系

无运行时依赖，仅使用标准库。

## 核心接口/API

### `Template`

可复用的已解析模板对象。

```rust
// 解析模板
let template = Template::parse("Hello, {{ name }}!")?;

// 查看占位符列表
let placeholders: Vec<&str> = template.placeholders().collect();

// 渲染模板
let result = template.render([("name", "Codex")])?;
assert_eq!(result, "Hello, Codex!");
```

### `render(template: &str, variables: I) -> Result<String, TemplateError>`

便捷函数，一步完成解析和渲染。

```rust
let result = render(
    "{{ greeting }}, {{ name }}!",
    [("greeting", "Hello"), ("name", "Codex")]
)?;
```

### 错误类型

#### `TemplateParseError`

- `EmptyPlaceholder` -- 占位符内容为空 `{{ }}`
- `NestedPlaceholder` -- 嵌套的 `{{`
- `UnmatchedClosingDelimiter` -- 未匹配的 `}}`
- `UnterminatedPlaceholder` -- 未闭合的 `{{`

#### `TemplateRenderError`

- `DuplicateValue` -- 变量值提供了多次
- `ExtraValue` -- 提供了模板中不存在的变量
- `MissingValue` -- 模板中的占位符缺少对应的变量值

#### `TemplateError`

统一错误类型，包含 `Parse` 和 `Render` 两个变体。
