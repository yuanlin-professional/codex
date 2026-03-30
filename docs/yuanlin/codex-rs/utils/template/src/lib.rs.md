# `lib.rs` — 最小化严格模板引擎

## 功能概述

提供用于提示和文本资产的最小化严格模板引擎。支持 `{{ name }}` 占位符插值、`{{{{` 转义为字面量 `{{`、`}}}}` 转义为字面量 `}}`。`Template::parse` 解析模板为可重用的 `Template` 结构体，`render` 方法接受变量映射进行渲染。严格验证：空占位符、嵌套占位符、未匹配的 `}}`、缺失/多余/重复变量均返回详细错误。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Template` | struct | 解析后的模板，可重复渲染 |
| `TemplateParseError` | enum | 解析错误：Empty/Nested/Unmatched/Unterminated |
| `TemplateRenderError` | enum | 渲染错误：Duplicate/Extra/Missing Value |
| `TemplateError` | enum | 统一错误：Parse 或 Render |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Template::parse` | `fn(source: &str) -> Result<Self, TemplateParseError>` | 解析模板字符串 |
| `Template::render` | `fn(&self, variables) -> Result<String, TemplateRenderError>` | 渲染模板 |
| `Template::placeholders` | `fn(&self) -> impl Iterator<Item = &str>` | 获取占位符名称集合 |
| `render` | `fn(template, variables) -> Result<String, TemplateError>` | 一步式解析+渲染便捷函数 |

## 依赖关系

- **引用的 crate**: `std::collections`
- **被引用方**: 系统提示构造、文本资产处理

## 实现备注

- 占位符名称两端的空白会被 trim。
- 变量映射使用 `BTreeMap` 确保确定性顺序。
- 测试覆盖了替换、复用、排序、多行、转义和各类错误场景。
