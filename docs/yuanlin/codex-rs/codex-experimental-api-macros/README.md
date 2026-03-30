# codex-experimental-api-macros

## 功能概述

`codex-experimental-api-macros` 是一个 Rust 过程宏（proc-macro）crate，为 Codex app-server 协议提供 `#[derive(ExperimentalApi)]` 宏。该宏用于在编译时自动为结构体和枚举生成实验性 API 检测逻辑，使得运行时可以判断某个请求/通知/参数是否使用了实验性功能。

主要功能包括：

- **实验性字段标记**：通过 `#[experimental("reason")]` 属性标记结构体字段或枚举变体为实验性的。
- **嵌套实验性检测**：通过 `#[experimental(nested)]` 属性递归检查嵌套类型的实验性状态。
- **运行时检测**：为标记的类型生成 `ExperimentalApi::experimental_reason(&self) -> Option<&'static str>` 实现，在运行时检查值是否实际使用了实验性功能。
- **智能存在性检测**：对 `Option<T>`、`Vec<T>`、`HashMap/BTreeMap` 和 `bool` 类型智能判断字段是否"存在"（非 None、非空、非 false）。
- **静态字段注册**：通过 `inventory` crate 静态注册实验性字段信息，支持全局枚举所有实验性字段。
- **序列化名称映射**：自动将 snake_case 字段名转换为 camelCase，与 JSON 序列化保持一致。

## 架构说明

该 crate 是一个标准的 Rust proc-macro 库，编译时运行，生成两部分代码：

```
#[derive(ExperimentalApi)]
struct MyParams {
    #[experimental("my feature")]
    optional_field: Option<String>,
    #[experimental(nested)]
    nested_field: SubParams,
    normal_field: String,
}
```

生成的代码包括：

1. **`ExperimentalApi` trait 实现**：按字段顺序检查是否使用了实验性功能。
   - 对 `#[experimental("reason")]` 字段：检查字段是否"存在"（Option 非 None、Vec 非空等），若存在则返回 reason。
   - 对 `#[experimental(nested)]` 字段：递归调用嵌套类型的 `experimental_reason()` 方法。

2. **常量 `EXPERIMENTAL_FIELDS`**（仅结构体）：静态数组，包含该类型所有实验性字段的名称（camelCase）和原因。

3. **`inventory::submit!` 注册**（仅结构体）：全局注册实验性字段信息，支持运行时枚举。

对于枚举类型，生成按变体匹配的实现：

```rust
impl ExperimentalApi for MyEnum {
    fn experimental_reason(&self) -> Option<&'static str> {
        match self {
            Self::ExperimentalVariant { .. } => Some("reason"),
            Self::NormalVariant { .. } => None,
        }
    }
}
```

## 目录结构

```
codex-experimental-api-macros/
├── Cargo.toml
└── src/
    └── lib.rs    # 全部过程宏逻辑：
                  #   - derive_experimental_api()  入口
                  #   - derive_for_struct()       结构体处理
                  #   - derive_for_enum()         枚举处理
                  #   - 辅助函数：属性解析、存在性表达式生成、
                  #     类型判断（Option/Vec/Map/bool）、命名转换
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `proc-macro2` | 过程宏 token 流操作 |
| `quote` | Rust 代码生成（quasi-quoting） |
| `syn` | Rust 语法解析（full + extra-traits 特性） |

作为 proc-macro crate，依赖极为精简，仅需标准的宏开发三件套。

## 核心接口/API

### 派生宏

```rust
#[proc_macro_derive(ExperimentalApi, attributes(experimental))]
pub fn derive_experimental_api(input: TokenStream) -> TokenStream;
```

### 支持的属性

| 属性 | 用途 | 示例 |
|------|------|------|
| `#[experimental("reason")]` | 标记字段/变体为实验性 | `#[experimental("dynamic tools support")]` |
| `#[experimental(nested)]` | 递归检查嵌套类型 | `#[experimental(nested)]` |

### 生成的 trait 实现

```rust
// 由宏生成
impl ExperimentalApi for MyType {
    fn experimental_reason(&self) -> Option<&'static str> {
        // 按字段顺序检查实验性功能使用情况
    }
}

// 仅结构体
impl MyType {
    pub(crate) const EXPERIMENTAL_FIELDS: &'static [ExperimentalField] = &[...];
}
```

### 字段存在性判断规则

| 类型 | 判断逻辑 |
|------|----------|
| `Option<T>` | `.as_ref().is_some_and(...)` |
| `Vec<T>` | `!.is_empty()` |
| `HashMap<K,V>` / `BTreeMap<K,V>` | `!.is_empty()` |
| `bool` | 直接判断值 |
| 其他类型 | 始终视为"存在" |
